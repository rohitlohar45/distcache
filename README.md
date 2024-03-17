## DistCache: Distributed Cache for Go Applications

### Overview

DistCache is a distributed caching library for Go applications. It provides a simple and efficient way to cache data in memory across multiple nodes in a distributed system. DistCache uses a consistent hashing algorithm to distribute data evenly across a cluster of cache servers, ensuring high availability and scalability.

### Features

- **Distributed Cache**: DistCache allows you to distribute cached data across multiple nodes in a network, providing fault tolerance and scalability.
- **Consistent Hashing**: DistCache uses consistent hashing to evenly distribute data across cache nodes, minimizing cache hotspots and balancing load.

- **HTTP-Based Communication**: DistCache uses HTTP-based communication between cache nodes, making it easy to integrate with existing Go applications and other programming languages.

- **Cache Grouping**: DistCache supports cache grouping, allowing you to organize cached data into logical groups for easier management and retrieval.

### Getting Started

To use DistCache in your Go application, follow these steps:

1. **Install DistCache**:

   You can install DistCache using `go get`:

   ```bash
   go get github.com/rohitlohar45/distcache
   ```

2. **Import DistCache**:

   Import DistCache in your Go code:

   ```go
   import "distcache"
   ```

3. **Create a Cache Group**:

   Create a new cache group using the `NewGroup` function:

   ```go
   dist := distcache.NewGroup("todos", 2<<10, distcache.GetterFunc(
    // Getter function implementation
   ))
   ```

4. **Implement Getter Function**:

   Implement a getter function to fetch data from the database or another data source:

   ```go
   func(key string) ([]byte, error) {
     // Fetch data from the database or another data source
   }
   ```

5. **Start Cache Server**:

   Start the cache server using the `startCacheServer` function:

   ```go
   startCacheServer(addr string, addrs []string, dist *distcache.Group)
   ```

6. **Start API Server (Optional)**:

   If you want to expose cache functionality via an HTTP API, start the API server using the `startAPIServer` function:

   ```go
   startAPIServer(apiAddr string, dist *distcache.Group)
   ```

### Example Usage

Here's an example of how you can use DistCache in your Go application:

```go
package main

import (
    "distcache"
    "encoding/json"
    "log"
    "net/http"
    "time"

    "github.com/gorilla/mux"
)

// Todo represents a todo item
type Todo struct {
    ID        int       `json:"id"`
    Title     string    `json:"title"`
    Completed bool      `json:"completed"`
    CreatedAt time.Time `json:"created_at"`
    UpdatedAt time.Time `json:"updated_at"`
}

// Implement getter function to fetch todo from database
func getTodoFromDB(key string) ([]byte, error) {
    // Fetch todo from the database
}

func main() {
    // Create a new cache group
    dist := distcache.NewGroup("todos", 2<<10, distcache.GetterFunc(getTodoFromDB))

    // Start the cache server
    go startCacheServer("localhost:8001", []string{"http://localhost:8001", "http://localhost:8002", "http://localhost:8003"}, dist)

    // Start the API server
    startAPIServer("http://localhost:9999", dist)
}

func startAPIServer(apiAddr string, dist *distcache.Group) {
    r := mux.NewRouter()

    r.HandleFunc("/api/todos", func(w http.ResponseWriter, r *http.Request) {
        key := r.URL.Query().Get("key")

        // Fetch todo from cache
        view, err := dist.Get(key)
        if err != nil {
            // Handle error
        }

        // Respond with todo
        w.Header().Set("Content-Type", "application/json")
        w.Write(view.ByteSlice())
    }).Methods(http.MethodGet)

    log.Println("Frontend server is running at", apiAddr)
    log.Fatal(http.ListenAndServe(apiAddr[7:], r))
}
```

### Conclusion

DistCache is a powerful caching library for Go applications, providing distributed caching capabilities with ease of use and high performance. By following the steps outlined in this documentation, you can quickly integrate DistCache into your applications and take advantage of its features to improve performance and scalability.
