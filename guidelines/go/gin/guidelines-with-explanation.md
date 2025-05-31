# Golang - Gin Guidelines

Junie guidelines for developing go services with the [gin-gonic](https://gin-gonic.com) framework.

## 1. Organize Project Structure

* Follow a domain-driven or feature-based structure rather than organizing by technical layers
* Keep related functionality together to improve code discoverability
* Use a consistent naming convention for packages and files

**Explanation:**

* A well-organized project structure makes it easier to understand the codebase, locate files, and maintain the application over time.
* Domain or feature-based organization helps developers find all related code (handlers, services, models) in one place.
* A consistent structure reduces cognitive load when navigating the codebase.

```go
// Example project structure
project/
├── cmd/
│   └── main.go               // Application entry point
├── internal/                 // Private application code
│   ├── auth/                 // Auth feature
│   │   ├── handler.go        // HTTP handlers
│   │   ├── middleware.go     // Auth middleware
│   │   ├── service.go        // Business logic
│   │   └── repository.go     // Data access
│   ├── user/                 // User feature
│   └── product/              // Product feature
├── pkg/                      // Public libraries
│   ├── database/             // Database utilities
│   └── validator/            // Validation utilities
├── api/                      // API documentation
├── config/                   // Configuration files
└── go.mod                    // Go module definition
```

## 2. Dependency Injection with Explicit Construction

* Create service structs with explicit dependencies passed via constructors
* Avoid global variables or singletons
* Use interfaces to define dependencies for better testability

**Explanation:**

* Explicit dependency injection makes code more maintainable, testable, and readable
* Dependencies are clearly visible in function signatures rather than hidden in implementation
* This approach enables mocking dependencies for unit testing

```go
// user/repository.go
type Repository interface {
    FindByID(ctx context.Context, id string) (*User, error)
    // Other methods...
}

type postgresRepository struct {
    db *sql.DB
}

func NewRepository(db *sql.DB) Repository {
    return &postgresRepository{db: db}
}

// user/service.go
type Service struct {
    repo Repository
    logger *log.Logger
}

func NewService(repo Repository, logger *log.Logger) *Service {
    return &Service{
        repo: repo,
        logger: logger,
    }
}
```

## 3. Centralized Error Handling

* Define custom error types for different error categories
* Use middleware to catch and handle errors consistently
* Return structured error responses with appropriate HTTP status codes

**Explanation:**

* Consistent error handling improves user experience and makes debugging easier
* Centralized error handling avoids code duplication and ensures uniform error responses
* Structured error responses provide clear information to API consumers

```go
// pkg/errors/errors.go
type AppError struct {
    Type    string `json:"type"`
    Message string `json:"message"`
    Code    int    `json:"-"` // HTTP status code
}

func (e AppError) Error() string {
    return e.Message
}

// Predefined error types
var (
    ErrNotFound = func(msg string) AppError {
        return AppError{Type: "not_found", Message: msg, Code: http.StatusNotFound}
    }
    
    ErrBadRequest = func(msg string) AppError {
        return AppError{Type: "bad_request", Message: msg, Code: http.StatusBadRequest}
    }
    
    // Other error types...
)

// middleware/error_handler.go
func ErrorHandler() gin.HandlerFunc {
    return func(c *gin.Context) {
        c.Next()
        
        if len(c.Errors) > 0 {
            err := c.Errors.Last().Err
            
            var appErr AppError
            if errors.As(err, &appErr) {
                c.JSON(appErr.Code, gin.H{
                    "error": appErr,
                })
                return
            }
            
            // Handle unexpected errors
            c.JSON(http.StatusInternalServerError, gin.H{
                "error": gin.H{
                    "type": "internal_error",
                    "message": "An unexpected error occurred",
                },
            })
        }
    }
}
```

## 4. Secure Middleware Configuration

* Configure security-related middleware in the correct order
* Use HTTPS by default in production
* Implement proper CORS, CSP, and other security headers

**Explanation:**

* Security middleware protects your application from common attacks
* The order of middleware is crucial - some security protections must be applied before others
* Well-configured security headers protect against XSS, CSRF, and other common vulnerabilities

```go
func setupRouter() *gin.Engine {
    // Use release mode in production
    gin.SetMode(gin.ReleaseMode)
    
    r := gin.Default()
    
    // Recovery middleware recovers from panics
    r.Use(gin.Recovery())
    
    // Custom logger that doesn't log sensitive data
    r.Use(middleware.SecureLogger())
    
    // Set security headers
    r.Use(middleware.SecurityHeaders())
    
    // CORS configuration
    r.Use(middleware.ConfigureCORS())
    
    // Rate limiting
    r.Use(middleware.RateLimiter())
    
    // Error handling
    r.Use(middleware.ErrorHandler())
    
    // Request ID for tracing
    r.Use(middleware.RequestID())
    
    // Add routes...
    
    return r
}

// middleware/security.go
func SecurityHeaders() gin.HandlerFunc {
    return func(c *gin.Context) {
        // Prevent MIME type sniffing
        c.Header("X-Content-Type-Options", "nosniff")
        
        // Prevent clickjacking
        c.Header("X-Frame-Options", "DENY")
        
        // XSS protection
        c.Header("X-XSS-Protection", "1; mode=block")
        
        // Content Security Policy
        c.Header("Content-Security-Policy", "default-src 'self'")
        
        // HTTP Strict Transport Security (for HTTPS)
        c.Header("Strict-Transport-Security", "max-age=31536000; includeSubDomains")
        
        c.Next()
    }
}
```

## 5. Input Validation and Sanitization

* Validate all input data before processing
* Use a structured validation library compatible with Gin
* Sanitize inputs to prevent injection attacks

**Explanation:**

* Input validation is the first line of defense against many attacks
* Structured validation makes requirements clear and helps catch errors early
* Proper sanitization prevents SQL injection, XSS, and other injection attacks

```go
// user/handler.go
type CreateUserRequest struct {
    Username string `json:"username" binding:"required,alphanum,min=3,max=30"`
    Email    string `json:"email" binding:"required,email"`
    Password string `json:"password" binding:"required,min=12"`
}

func (h *Handler) CreateUser(c *gin.Context) {
    var req CreateUserRequest
	// Binding does the validation
    if err := c.ShouldBindJSON(&req); err != nil {
        c.Error(errors.ErrBadRequest("Invalid input: " + err.Error()))
        return
    }
    
    // Sanitize inputs to prevent XSS
    req.Username = bluemonday.StrictPolicy().Sanitize(req.Username)
    req.Email = bluemonday.StrictPolicy().Sanitize(req.Email)
    
    // Process the validated and sanitized request...
    
    // Return response
    c.JSON(http.StatusCreated, gin.H{"message": "User created"})
}
```

## 6. Secure Authentication and Authorization

* Use JWT or sessions with proper security configurations
* Don't store and use passwords, instead rely on OAuth2 and OIDC protocols
* If you must store passwords, use strong hashing algorithm (bcrypt/argon2)
* Implement proper authorization checks at every secured endpoint

**Explanation:**

* Authentication verifies user identity; authorization verifies permissions
* Secure storage of credentials is essential to prevent data breaches
* Best way would be to not store any user-credentials directly but instead rely on OAuth2 Identity Providers to handle Authentication for you.
* Authorization checks must be consistent across all endpoints

## 7. Database Access Best Practices

* Use prepared statements to prevent SQL injection
* Implement context-aware database calls
* Apply proper database connection management

**Explanation:**

* Prepared statements protect against SQL injection attacks
* Context-aware calls allow for proper timeout and cancellation
* Proper connection management prevents resource leaks

```go
// user/repository.go
func (r *postgresRepository) FindByID(ctx context.Context, id string) (*User, error) {
    // Use prepared statement with placeholder
    query := "SELECT id, username, email, created_at FROM users WHERE id = $1"
    
    var user User
    err := r.db.QueryRowContext(ctx, query, id).Scan(
        &user.ID,
        &user.Username,
        &user.Email,
        &user.CreatedAt,
    )
    
    if err != nil {
        if err == sql.ErrNoRows {
            return nil, errors.ErrNotFound("User not found")
        }
        return nil, fmt.Errorf("database error: %w", err)
    }
    
    return &user, nil
}

// main.go
func setupDatabase() (*sql.DB, error) {
    db, err := sql.Open("postgres", os.Getenv("DATABASE_URL"))
    if err != nil {
        return nil, err
    }
    
    // Set connection pool parameters
    db.SetMaxOpenConns(25)
    db.SetMaxIdleConns(5)
    db.SetConnMaxLifetime(5 * time.Minute)
    
    // Test connection
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    
    if err := db.PingContext(ctx); err != nil {
        return nil, err
    }
    
    return db, nil
}
```

## 8. Structured Logging

* Use a structured logging library (e.g.: slog)
* Include contextual information in logs (e.g.: traceId)
* Avoid logging sensitive information

**Explanation:**

* Structured logs are easier to parse and analyze
* Contextual information makes troubleshooting more efficient
* Secure logging prevents leaking sensitive data

```go
// ConfigureLogger sets up the global logger with the specified log level from environment
func ConfigureLogger() {
	// Parse log level from environment
	env := os.Getenv("LOG_LEVEL")
	var level slog.Level
	switch strings.ToLower(env) {
	case "debug":
		level = slog.LevelDebug
	case "info":
		level = slog.LevelInfo
	case "warn":
		level = slog.LevelWarn
	case "error":
		level = slog.LevelError
	default:
		level = slog.LevelInfo
	}

	// Create logger with timestamp and level
	logger := slog.New(slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{
		Level: level,
	}))

	// Set as default logger
	slog.SetDefault(logger)
	slog.Info("logging level set", "level", level.String())
}
```

## 9. API Design and Response Structure

* Define consistent response formats
* Use appropriate HTTP status codes
* Include pagination for list endpoints

**Explanation:**

* Consistent responses make APIs easier to consume
* Proper HTTP status codes communicate intent clearly
* Pagination prevents performance issues with large datasets

## 10. Effective Testing

* Write unit tests for business logic
* Use Go's testing package and testify for assertions
* Implement integration tests for critical paths

**Explanation:**

* Tests ensure code correctness and prevent regressions
* Unit tests focus on business logic without external dependencies
* Integration tests verify that components work together correctly

```go
// user/service_test.go
func TestUserService_CreateUser(t *testing.T) {
    // Create a mock repository
    mockRepo := new(mocks.Repository)
    
    // Set expectations
    mockRepo.On("FindByEmail", mock.Anything, "test@example.com").
        Return(nil, errors.ErrNotFound("User not found"))
    
    mockRepo.On("Create", mock.Anything, mock.MatchedBy(func(u *User) bool {
        return u.Email == "test@example.com" && u.Username == "testuser"
    })).Return("user-id", nil)
    
    // Create service with mock dependencies
    service := NewService(mockRepo, log.New(os.Stdout, "", 0))
    
    // Call the method being tested
    id, err := service.CreateUser(context.Background(), CreateUserInput{
        Username: "testuser",
        Email:    "test@example.com",
        Password: "securepassword",
    })
    
    // Assert results
    assert.NoError(t, err)
    assert.Equal(t, "user-id", id)
    
    // Verify expectations were met
    mockRepo.AssertExpectations(t)
}

// integration_test.go
func TestUserAPI_Integration(t *testing.T) {
    // Skip if not running integration tests
    if testing.Short() {
        t.Skip("Skipping integration tests")
    }
    
    // Setup test database and server
    db := setupTestDatabase(t)
    router := setupRouter(db)
    
    // Create test server
    ts := httptest.NewServer(router)
    defer ts.Close()
    
    // Test creating a user
    resp, body := testRequest(t, ts, "POST", "/api/v1/users", map[string]interface{}{
        "username": "integrationtest",
        "email":    "integration@test.com",
        "password": "secure-password-123",
    })
    
    assert.Equal(t, http.StatusCreated, resp.StatusCode)
    
    var response map[string]interface{}
    err := json.Unmarshal(body, &response)
    assert.NoError(t, err)
    
    data := response["data"].(map[string]interface{})
    assert.Equal(t, "integrationtest", data["username"])
    assert.Equal(t, "integration@test.com", data["email"])
    assert.NotContains(t, data, "password")
}
```

## 11. Configuration Management

* Use environment variables for configuration
* Implement secure handling of secrets
* Provide sensible defaults

**Explanation:**

* Environment variables are the standard for configuration in containers
* Secrets should never be hardcoded or committed to version control
* Sensible defaults make the application easier to run

```go
type Config struct {
	Enabled          bool   `mapstructure:"enabled"`
	Broker           string `mapstructure:"broker"`
	ConnectionString string `mapstructure:"connection_string"`
	ClientID         string `mapstructure:"client_id"`
	Topic            string `mapstructure:"topic"`
}

// PrepareEnvironment loads the correct config yaml file
func PrepareEnvironment(profile string) (Config, error) {
	var cfg Config
	viper.SetConfigName(profile)
	viper.SetConfigType("yaml")
	viper.AutomaticEnv()
	viper.SetEnvKeyReplacer(strings.NewReplacer(`.`, `__`))

	data, err := config.Dir.ReadFile(fmt.Sprintf("yaml/%s.yaml", profile))
	if err != nil {
		return cfg, err
	}

	if err := viper.ReadConfig(bytes.NewReader(data)); err != nil {
		return cfg, err
	}
	if err := viper.Unmarshal(&cfg); err != nil {
		return cfg, err
	}

	return cfg, nil
}
```

## 12. Context Propagation

* Use context for request scoped values and cancellation
* Propagate context through all layers of the application
* Set appropriate timeouts

**Explanation:**

* Context propagation ensures proper request handling and cancellation
* Request-scoped values (user ID, tracing ID) should be passed via context
* Timeouts prevent long-running operations from consuming resources

```go
// user/handler.go
func (h *Handler) GetUser(c *gin.Context) {
    // Extract user ID from URL
    userID := c.Param("id")
    
    // Create context with timeout
    ctx, cancel := context.WithTimeout(c.Request.Context(), 3*time.Second)
    defer cancel()
    
    // Call service with context
    user, err := h.service.GetUserByID(ctx, userID)
    if err != nil {
        c.Error(err)
        return
    }
    
    response.Success(c, http.StatusOK, user)
}

// user/service.go
func (s *Service) GetUserByID(ctx context.Context, id string) (*User, error) {
    // Log with request context
	log.Ctx(ctx).
		Info().
		Str("user_id", id).
		Msg("Getting user by ID")
    
    // Use the same context for repository call
    user, err := s.repo.FindByID(ctx, id)
    if err != nil {
        return nil, err
    }
    
    return user, nil
}
```

## 13. Graceful Shutdown

* Implement graceful shutdown to handle in-flight requests
* Close resources properly when shutting down
* Use appropriate timeouts for shutdown

**Explanation:**

* Graceful shutdown ensures in-flight requests are completed
* Proper resource cleanup prevents leaks
* Shutdown timeouts prevent hanging during termination

```go
func main() {
  router := gin.Default()
  router.GET("/", func(c *gin.Context) {
    time.Sleep(5 * time.Second)
    c.String(http.StatusOK, "Welcome Gin Server")
  })

  srv := &http.Server{
    Addr:    ":8080",
    Handler: router.Handler(),
  }

  go func() {
    // service connections
    if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
      log.Fatalf("listen: %s\n", err)
    }
  }()

  // Wait for interrupt signal to gracefully shutdown the server with
  // a timeout of 5 seconds.
  quit := make(chan os.Signal, 1)
  // kill (no params) by default sends syscall.SIGTERM
  // kill -2 is syscall.SIGINT
  // kill -9 is syscall.SIGKILL but can't be caught, so don't need add it
  signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
  <-quit
  log.Println("Shutdown Server ...")

  ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
  defer cancel()
  if err := srv.Shutdown(ctx); err != nil {
    log.Println("Server Shutdown:", err)
  }
  // catching ctx.Done(). timeout of 5 seconds.
  <-ctx.Done()
  log.Println("timeout of 5 seconds.")
  log.Println("Server exiting")
}
```
