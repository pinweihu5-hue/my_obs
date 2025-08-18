[[go_lang_idx]]


```bash


# Open your terminal and navigate to the directory where you want to create your new Go project
cd ~/path/to/your/directory

# Create a new directory for your project and navigate into it
mkdir my-go-project && cd my-go-project

# Initialize the Go module
go mod init github.com/your-username/my-go-project

# create a main file
touch main.go


# inside main.go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}




# to run it
go run main.go

# to build it to binary
go build















```