# <p align="center"> ft_vdprintf - Core Variadic Printf Implementation </p>

**42 Project | Core Printf Implementation**  
**Objective:** Implement the core variadic `vdprintf` function that serves as the foundation for both `ft_printf` and `ft_dprintf`.

---

## Overview

**ft_vdprintf** is the core implementation of a custom printf library. It accepts a `va_list` parameter, making it the foundation for all variadic printf functions. Both `ft_printf` and `ft_dprintf` are thin wrappers that convert their variadic arguments to `va_list` and call `ft_vdprintf`.

**Development Context:** This implementation was developed for `minishell` to print error messages to `stderr` instead of `stdout`.

---

## Skills Demonstrated

- **Variadic Functions:** Handling `va_list` parameters for flexible function composition
- **File Descriptor I/O:** Writing to arbitrary file descriptors using `write()`
- **Format String Parsing:** Complex parsing of flags, width, precision, specifiers
- **Type Conversion:** Converting various data types to formatted strings
- **Memory Management:** Efficient buffer handling for conversions
- **Platform Compatibility:** Cross-platform NULL pointer handling
- **Modular Architecture:** Clean separation allowing multiple wrappers

---

## Project Structure

```
ft_vdprintf/
├── ft_vdprintf.c      # Main vdprintf function
├── ft_vdprintf.h      # Header with all declarations and macros
├── ft_parser.c        # Format string parsing and specifier handling
├── ft_print_types.c   # Character and string printing functions
├── ft_print_int.c     # Integer conversion and formatting
├── ft_printf_utils.c  # Helper functions (atoi, strlen, etc.)
└── Makefile           # Build configuration
```

**Key Files:**
- **ft_vdprintf.c** - Main entry point, handles format string iteration and calls parser/handlers
- **ft_parser.c** - Parses format string, extracts flags/width/precision/specifier
- **ft_print_types.c** - Handles `%c` and `%s` specifiers
- **ft_print_int.c** - Handles all numeric specifiers (`%d`, `%i`, `%u`, `%x`, `%X`, `%p`)

---

## Function Prototypes

```c
// Core variadic printf function
int ft_vdprintf(int fd, const char *fmt, va_list ap);
```

**Parameters:**
- `fd`: File descriptor to write to (e.g., `STDOUT_FILENO`, `STDERR_FILENO`, or custom file descriptor)
- `fmt`: Format string with specifiers
- `ap`: Variable argument list (`va_list`) containing the arguments to format

**Returns:**
- Number of characters written
- -1 on error

**Usage:** This function is typically called by wrapper functions like `ft_printf` or `ft_dprintf` that handle the variadic argument conversion.

---

## Supported Features

### Format Specifiers
- `%c` - Character
- `%s` - String
- `%p` - Pointer address (with platform-specific NULL handling)
- `%d` / `%i` - Signed integer
- `%u` - Unsigned integer
- `%x` - Lowercase hexadecimal
- `%X` - Uppercase hexadecimal
- `%%` - Literal percent sign

### Flags
- `-` - Left alignment
- `+` - Always show sign for numbers
- ` ` (space) - Space for positive numbers
- `0` - Zero padding
- `#` - Alternative format (0x for hex, etc.)

### Width & Precision
- Minimum field width
- Precision for numbers and strings
- Dynamic width/precision from arguments

---

## Technical Implementation

### Core Components

**1. Format Parser ([`ft_parser.c`](ft_parser.c))**
- Parses format string character by character
- Extracts flags, width, precision, and specifier
- Handles edge cases and invalid formats
- Returns a `t_fmt` structure with parsed information

**2. Type Handlers ([`ft_print_types.c`](ft_print_types.c))**
- [`ft_print_char`](ft_print_types.c#L23): Single character output
- [`ft_print_str`](ft_print_types.c#L48): String output with width/precision
- Handles NULL pointers and empty strings

**3. Integer Conversion ([`ft_print_int.c`](ft_print_int.c))**
- Converts integers to strings in various bases
- Handles signed/unsigned correctly
- Formats with flags, width, and precision
- Efficient buffer management
- Supports all numeric specifiers (`%d`, `%i`, `%u`, `%x`, `%X`, `%p`)

**4. Utilities ([`ft_printf_utils.c`](ft_printf_utils.c))**
- Helper functions for parsing and conversion
- String manipulation utilities (`ft_strlen`, `ft_strchr`)
- Number parsing functions (`ft_atoi_positive`, `ft_isdigit`)
- Memory utilities (`ft_memset`)

**5. Main Function ([`ft_vdprintf.c`](ft_vdprintf.c))**
- Iterates through format string
- Handles literal text (non-format characters)
- Calls parser for format specifiers
- Dispatches to appropriate handler based on specifier
- Manages return value and error handling

---

## Platform-Specific Behavior

### NULL Pointer Handling

The `%p` specifier outputs NULL pointers differently on Linux vs macOS:

```c
#ifdef __linux__
#  define NULL_PTR_STR "(nil)"
#else
#  define NULL_PTR_STR "0x0"
#endif
```

This ensures compatibility with platform-specific printf behavior.

---

## Architecture

### Modular Design

```
ft_vdprintf/         (Core implementation)
├── ft_vdprintf.c    - Main function, format string iteration
├── ft_parser.c      - Format parsing logic
├── ft_print_types.c - Character/string handlers
├── ft_print_int.c   - Integer conversion and formatting
└── ft_printf_utils.c - Helper utilities

ft_printf/           (Wrapper)
└── ft_printf.c      - Calls vdprintf with STDOUT_FILENO

ft_dprintf/          (Wrapper)
└── ft_dprintf.c     - Calls vdprintf with user-specified fd
```

**Design Philosophy:**
- `ft_vdprintf` provides the core functionality with file descriptor support
- Both `ft_printf` and `ft_dprintf` are thin wrappers that call `ft_vdprintf`
- This modular approach allows code reuse and easier maintenance

---

## Compilation

```bash
# Build the library
make

# Link with your program
gcc your_program.c -L./ft_vdprintf -lftvdprintf -o your_program
```

---

## Integration with Other Projects

This library is used by:
- **ft_printf:** Wraps `ft_vdprintf` with `STDOUT_FILENO` (see `ft_printf/README.md`)
- **ft_dprintf:** Wraps `ft_vdprintf` with a user-specified file descriptor (see `ft_dprintf/README.md`)

Both wrappers convert their variadic arguments (`...`) to `va_list` and call `ft_vdprintf`.

---

## Technical Highlights

- **Efficient I/O:** Direct use of `write()` system call for maximum control
- **Memory Safety:** Proper handling of NULL pointers and edge cases
- **Format Flexibility:** Supports all standard printf flags and modifiers
- **Platform Compatibility:** Handles platform-specific differences gracefully
- **Modular Design:** Clean separation of concerns for maintainability
- **Variadic Support:** Uses `va_list` for flexible function composition
- **Error Handling:** Returns -1 on write errors, handles overflow conditions

---

*This project demonstrates advanced C programming including system calls, variadic functions, complex parsing, and modular library architecture.*
