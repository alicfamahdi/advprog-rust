# advprog-rust

<html lang="en">
<details>
<summary>Commit 1 Reflection</summary>

1. You may need to check the Rust documentation to understand what is inside the handle_connection method. Write as reflection notes in the Readme.md. Write it nicely.

What is inside the handle_connection method?
I'll start from the function signature:

```rust
fn handle_connection(mut stream: TcpStream) {
```
handle_connection takes a variable `stream` with the type `TcpStream` as an argument, which is a connection to a client. The `mut` keyword means that handle_connection can modify `stream`.

```rust
let buf_reader = BufReader::new(&mut stream);
```
Binding the `BufReader::new(&mut stream)` value to the `buf_reader` variable. `BufReader::new(&mut stream)` creates a buffered reader around `stream`.
`BufReader` is used to help improve efficiency by reading data in chunks instead of reading byte by byte. `&mut stream` is referenced to ensure that `BufReader` can still read from `TcpStream`.

```rust
let http_request: Vec<_> = buf_reader
    .lines()
    .map(|result| result.unwrap())
    .take_while(|line| !line.is_empty())
    .collect();
```
`.lines()` returns an iterator over lines from the stream. 
`.map(|result| result.unwrap())`is used because each line read is wrapped in a `Result<String, Error>`, so `.unwrap()` is used to extract the `String`, assuming it is valid.
`.take_while(|line| !line.is_empty())` keeps reading lines until an empty line (`""`) is encountered. In an HTTP request, an empty line separates the headers from the body.
`.collect()` collects the processed lines into a `Vec<String>`.

In summary, it reads the HTTP request line by line, storing the request method, URL, and headers in a `Vec<String>` in the `http_request` variable. It stops reading when it encounters an empty line, which marks the end of the request headers.

Then, the last line:
```rust
println!("Request: {:#?}", http_request);
```
`println!` prints the collected HTTP request lines in an organized format (`{:#?}`). This helps in debugging by showing the full request received from the client.
</details>

<details>
<summary>Commit 2 Reflection</summary>
<img src="commit2.png" alt="html page message"> 

2. You may need to read more regarding some of text that your program should write to the browser such “Content-Length” and others, check the chapter 20 or other resources on that. Write your own reflection of what you have learned about the new code the handle_connection.

The new code no longer just reads the request and prints it—now it also sends an HTTP response. The function now reads an HTML file (`hello.html`) and sends its contents back as the response body.

The code for reading the request is the same as before:
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let http_request: Vec<_> = buf_reader
        .lines()
        .map(|result| result.unwrap())
        .take_while(|line| !line.is_empty())
        .collect();
```

```rust
let status_line = "HTTP/1.1 200 OK";
```
Defines the HTTP status line, which tells the client that the request was successful (`200 OK`).

```rust
let contents = fs::read_to_string("hello.html").unwrap();
```
Reads the file `"hello.html"` from disk. `.unwrap()` assumes the file exists and will crash the program if it doesn't.

```rust
let length = contents.len();
```
Calculates the length of the file in bytes (needed for the `Content-Length` header).

```rust
let response =
    format!("{status_line}\r\nContent-Length:
{length}\r\n\r\n{contents}");
```
Constructs the full HTTP response:
  ```
  HTTP/1.1 200 OK
  Content-Length: <file size>
  
  <file contents>
  ```
`\r\n` (carriage return + newline) separates HTTP headers properly. The extra `\r\n\r\n` marks the end of the headers before sending the body.

```rust
stream.write_all(response.as_bytes()).unwrap();
```
Converts the response into bytes and writes it to the `stream`, sending it to the client. `.unwrap()` ensures the function panics if sending fails.
</details>

</html>

