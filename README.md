# Hermes ![Stars](https://img.shields.io/github/stars/realTristan/Hermes?color=brightgreen) ![Watchers](https://img.shields.io/github/watchers/realTristan/Hermes?label=Watchers)
![banner](https://user-images.githubusercontent.com/75189508/230987049-665418b1-3576-49b7-861e-29036859ad8a.png)

# About
## Storing Data
Hermes works by iterating over the items in the data.json file, and then iterates over the keys and values of the items and splits the value into different words. It then stores the indices for all of the items that contain those words in a dictionary.

## Accessing Data
When searching for a word, Hermes will return a list of indices for all of the items that contain that word. It checks whether the key in the cache dictionary contains the provided word, instead of just accessing it so that short forms for words can be used.

## How to improve the speed
1. Instead of iterating over all of the keys in the cache and checking whether they contain the word you're looking for, just immediately access the indices by map index. ex: return cache[word] instead of for(keys in cache) if key contains word...

2. Create a history cache

## Benchmarks
### Dataset
**Keys**: 4,115

**Total Words**: 208,092

**Map Size**: 33,048 bytes

<br>

### Speeds
**Python + Flask**: 436.27µs

**Golang + net/http**: 41.054µs

**Rust + actixweb**: 590.456µs


# Example
```go
////////////////////////////////////////////////////////////////
//
// Run Command: go run .
//
// Host URL: http://localhost:8000/courses?q=computer&limit=100
//
////////////////////////////////////////////////////////////////
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
	"strconv"
	"time"
)

// Initialize the cache from the hermes.go file
var cache *Cache = InitCache("data.json")

// Main function
func main() {
	// Print host
	fmt.Println(" >> Listening on: http://localhost:8000/")

	// Listen and serve on port 8000
	http.HandleFunc("/courses", Handler)
	http.ListenAndServe(":8000", nil)
}

// Handle the incoming http request
func Handler(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Access-Control-Allow-Origin", "*")

	// Get the query parameter
	var query string = r.URL.Query().Get("q")

	// parse the limit
	var limit int = 10
	if _limit := r.URL.Query().Get("limit"); _limit != "" {
		limit, _ = strconv.Atoi(_limit)
	}

	// Track the start time
	var start time.Time = time.Now()

	// Search for a word in the cache
	var courses []map[string]string = cache.Search(query, limit)

	// Print the duration
	fmt.Printf("\nFound %v results in %v", len(courses), time.Since(start))

	// Write the courses to the json response
	var response, _ = json.Marshal(courses)
	w.Write(response)
}
```

# License
MIT License

Copyright (c) 2023 Tristan

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
