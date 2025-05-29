# Get Next Line 📜

`get_next_line` is a C function that reads a line from a file descriptor. This project is a fundamental part of the 42 School curriculum, designed to teach students about static variables, memory allocation, file manipulation, and robust error handling while working with system calls like `read()`.

## 📋 Table of Contents

- [About](#about)
- [Function Prototype](#function-prototype)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Return Value](#return-value)
- [Key Concepts](#key-concepts)
- [Project Structure](#project-structure)
- [Testing](#testing)
- [Error Handling](#error-handling)
- [Author](#author)
- [Notes](#notes)

## 🎯 About

The `get_next_line` project challenges students to write a function that returns one line at a time from a given file descriptor. A "line" is defined as a sequence of characters ending with a newline character (`\n`) or the End-Of-File (EOF).

### Project Goals:
- Read text from a file descriptor, one line at a time.
- Manage static variables effectively to keep track of read data across multiple calls for the same file descriptor.
- Handle multiple file descriptors simultaneously without interference.
- Efficiently manage buffer sizes (defined by `BUFFER_SIZE` macro).
- Correctly handle EOF and read errors.
- Ensure no memory leaks.

## 📜 Function Prototype

The function prototype is typically:

```c
char *get_next_line(int fd);
```

### Parameters:
- `int fd`: The file descriptor to read from.

## ✨ Features

- **Line-by-Line Reading**: Reads a file descriptor and returns lines ending with `\n` or EOF.
- **Static Variable Management**: Uses static variables to store remaining data from previous reads for a given file descriptor.
- **Multiple File Descriptor Support**: Can handle calls from different file descriptors concurrently, maintaining separate buffers/states for each.
- **Configurable Buffer Size**: The size of the read buffer is determined by the `BUFFER_SIZE` compile-time macro.
- **EOF Handling**: Correctly returns the last line (if any) and signals EOF appropriately on subsequent calls.
- **Error Detection**: Handles errors from the `read()` system call.
- **Memory Management**: Allocates and frees memory as needed, preventing leaks.

## 🚀 Installation

1.  **Clone the repository**
    ```bash
    git clone https://github.com/Marouane0107/get_next_line.git
    cd get_next_line
    ```

2.  **Compile the project**
    Usually, `get_next_line` is compiled as part of a larger project or with a test `main.c`. To compile your `get_next_line` files (and any utility files) into an object file, or as part of a test program:
    ```bash
    # Example: Compiling with a test main.c (you'll need to create this)
    # Assuming BUFFER_SIZE is passed as a compile-time flag
    cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c -o gnl_tester
    ```
    If you have a `Makefile` for testing:
    ```bash
    make
    ```

    Common Makefile targets (if provided for testing):
    ```bash
    # Clean object files
    make clean

    # Full clean (object files and test executable)
    make fclean

    # Recompile
    make re
    ```

## 🎮 Usage

To use `get_next_line` in your C code:

1.  Include the header file: `#include "get_next_line.h"`
2.  Compile your project, linking the `get_next_line.c` (and `get_next_line_utils.c` if applicable) files or a compiled library.
3.  Call the function in a loop to read all lines from a file descriptor.

### Example `main.c`:

```c
#include "get_next_line.h" // Make sure this path is correct
#include <fcntl.h>         // For open()
#include <stdio.h>         // For printf()
#include <unistd.h>        // For close()

int main(void) {
    int     fd;
    char    *line;

    // Example: Reading from a file named "test.txt"
    fd = open("test.txt", O_RDONLY);
    if (fd == -1) {
        perror("Error opening file");
        return (1);
    }

    printf("Reading from file descriptor %d with BUFFER_SIZE %d:\n", fd, BUFFER_SIZE);
    while ((line = get_next_line(fd)) != NULL) {
        printf("%s", line); // Line includes newline if present
        free(line);         // Free the memory allocated by get_next_line
        line = NULL;
    }
    // The last call might return NULL if EOF is reached or an error occurs.

    close(fd);

    // Example: Reading from standard input (fd = 0)
    // printf("\nReading from standard input (fd 0):\n");
    // while ((line = get_next_line(0)) != NULL) {
    //     printf("Line: %s", line);
    //     free(line);
    //     line = NULL;
    // }

    return (0);
}
```

## ⬅️ Return Value

- **`char *`**:
    - Returns the line that was read (a null-terminated string).
    - The returned line includes the newline character (`\n`) if it was present in the file, except if EOF is reached and the file does not end with a newline.
- **`NULL`**:
    - If an error occurs during reading.
    - If EOF is reached and there are no more characters to read.

## 🔑 Key Concepts

- **Static Variables**: A static variable (often an array of character pointers or a more complex structure) is used to store the "leftover" characters from the `read()` call that were not part of the returned line. This ensures that on the next call to `get_next_line` for the same `fd`, these characters are processed first.
- **Buffer Management**: The `read()` system call reads `BUFFER_SIZE` bytes at a time. `get_next_line` must process this buffer, find a newline or EOF, and manage any remaining characters.
- **Dynamic Memory Allocation**: Lines are dynamically allocated using `malloc()`. It is the responsibility of the caller to `free()` this memory.
- **File Descriptors (FD)**: The function must work correctly with any valid file descriptor, including standard input (0), files, and potentially pipes. It must also handle multiple FDs without mixing their data, often by using an array of static buffers indexed by `fd` (or a linked list of buffers if `OPEN_MAX` is too large or dynamic FD handling is required).

## 📁 Project Structure

A common file structure for `get_next_line`:

```
get_next_line/
├── get_next_line.c          # Main logic for the get_next_line function
├── get_next_line_utils.c    # Utility functions (e.g., string manipulation like strjoin, strchr, substr)
├── get_next_line.h          # Header file with function prototype, BUFFER_SIZE definition (or it's a compile flag)
└── Makefile                 # Optional: For compiling tests or the library
```

## 🧪 Testing

Thorough testing is essential:
- **Different `BUFFER_SIZE` values**:
    - `BUFFER_SIZE = 1`: Tests character-by-character processing.
    - `BUFFER_SIZE = <length of a line>`: Tests reading a whole line in one go.
    - `BUFFER_SIZE > <length of a line>`: Tests handling leftover characters.
    - `BUFFER_SIZE < <length of a line>`: Tests accumulating parts of a line.
    - Large `BUFFER_SIZE` (e.g., 1024, 8192).
- **File Content Variations**:
    - Empty file.
    - File with no newline characters.
    - File with only newline characters (`\n\n\n`).
    - File ending with a newline.
    - File not ending with a newline.
    - Very long lines.
    - Standard input (typing text manually).
    - Binary files (though `get_next_line` is primarily for text).
- **Multiple File Descriptors**:
    - Reading from two or more files alternately to ensure static data is managed correctly per fd.
- **Error Conditions**:
    - Invalid file descriptor (e.g., -1).
    - Reading from a closed file descriptor (behavior might be tested if `read` errors are handled).

## ⚠️ Error Handling

The function must gracefully handle:
- **Read Errors**: If `read()` returns -1. `get_next_line` should return `NULL` and free any allocated memory for the current line.
- **Memory Allocation Failures**: If `malloc()` fails, `get_next_line` should return `NULL`.
- **Invalid File Descriptor**: If `fd < 0` or `fd > OPEN_MAX` (or a reasonable limit), or if `BUFFER_SIZE <= 0`.

## 👨‍💻 Author

**Marouane Aouzal** (Marouane0107)
- GitHub: [@Marouane0107](https://github.com/Marouane0107)
- UM6P-1337 Coding School Student

## 📝 Notes

- This project is a classic exercise in C programming, focusing on careful memory and file descriptor management.
- The `BUFFER_SIZE` macro is crucial and its value can significantly impact performance and testing scenarios.
- No memory leaks are permitted. All dynamically allocated memory must be freed appropriately.
- Adherence to the 42 School coding standards (Norminette) is typically required.

---

*Developed as part of the 42 School "get_next_line" project.*

*Last Updated: 2025-05-29*
