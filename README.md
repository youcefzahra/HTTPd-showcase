# 🌐 HTTPd - C Web Server

> **Showcase Repository:** To comply with EPITA's anti-plagiarism regulations, the source code is kept private. This repository serves as a technical showcase. A compiled version is available in the Releases section for testing.

## 📖 Project Context

**HTTPd** is a core system programming project aimed at building a fully functional HTTP server from scratch using standard C POSIX libraries and the Socket API. The goal is to manipulate low-level network streams (TCP/IP), handle the HTTP protocol, and manage background processes (daemons).

## ✨ Implemented Features

* **Network Communication:** Native implementation of TCP sockets using `socket`, `bind`, `listen`, and `accept` to handle incoming client connections.
* **HTTP Protocol Handling:** Parsing of incoming HTTP requests and formatting of HTTP responses (headers, status codes, payloads). Full support for `GET` and `HEAD` methods.
* **Daemonization:** The server can run as a background daemon process, detaching from the terminal to run autonomously.
* **Configuration Parsing:** Dynamically loads settings from a `.conf` file. Supports defining log files, PID files, and setting up Virtual Hosts (vhosts) with distinct server names, ports, and root directories.
* **Activity Logging:** Comprehensive logging module to trace incoming requests, server errors, and operational statuses.

## 🏗️ Technical Architecture

1. **Server Lifecycle & Daemon:** The `main` entry point parses CLI arguments and the configuration file. If daemon mode is triggered, it forks the process to detach it from the terminal.
2. **Socket & Event Loop:** The server continuously listens on the configured ports. Upon accepting a connection, the payload is handed over to the HTTP module.
3. **HTTP Engine:** Tokenizes and validates the incoming HTTP raw string, resolves the requested resource mapped to the server's root directory, and generates the proper HTTP payload (Headers + Body) to send back through the socket.

## 🎯 How to test the project?

A pre-compiled version of the server and its configuration wrapper are available for testing. Note that the C binary relies on a provided Bash wrapper script to parse `.conf` files.

1. Go to the **[Releases](../../releases)** section and download the `HTTPd-provided_files.tar.gz` archive.
2. Extract the provided files:

```bash
tar -xvf HTTPd-provided_files.tar.gz
```

**Terminal 1 - Setup:** Create a simple text file to serve.
```bash
echo "Hello from the first terminal" > hello.txt
```

**Terminal 1 - Start the daemon:** Use bash to execute the wrapper and start the server in the background.
```bash
chmod +x httpd
bash ./tests/config_reader.sh --path-bin ./httpd --path-config server.conf --daemon start
```
*Expected behavior: The terminal hands back control immediately. A test.pid file is created containing the child process PID.*

**Terminal 1 - Verify the process:** Check that the daemon is actively running.
```bash
cat test.pid
ps -p $(cat test.pid)
```

**Terminal 2 - Test the request:** Open a second terminal and fetch the file you created.
```bash
curl -v [http://127.0.0.1:8080/hello.txt](http://127.0.0.1:8080/hello.txt)
```
*Expected behavior: HTTP/1.1 200 OK response displaying "Hello from the first terminal". The connection closes cleanly.*

**Terminal 1 - Stop the daemon:** Go back to the first terminal and shut down the server.
```bash
bash ./tests/config_reader.sh --path-bin ./httpd --path-config server.conf --daemon stop
```
