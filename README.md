# advprog-rust

<html lang="en">
<details>
<summary>Commit 1 Reflection</summary>
You may need to check the Rust documentation to understand what is inside the
handle_connection method. Write as reflection notes in the Readme.md. Write it nicely.

- What is inside the handle_connection method?
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
</html>

