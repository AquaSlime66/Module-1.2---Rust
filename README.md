# Overview
The goal of this project was to learn the basics of the Rust programming langauge. A side goal was also attempting to understand how applications can access and interact with live websites.
This software is effectively a website scraper, where the user can choose one of four items to scrape. These include the website's paragraph tag content, a href tag content, script links or lines, and img links. Once they've chosen an item to scrape, the program asks for a new (for the file), and a URL to scrape from. 
The majority of work on this project revolved around learning how different types of scrapers work (i.e., the content of a tag vs. a link stored in a tag), and how data conversion works in Rust. This data conversion mostly revolved around html to strings, strings to URLs, and similar conversions.

[Rust Web Scraper Demo Video](https://www.youtube.com/watch?v=I9DjGnXWKP8)

# Development Environment

### Tools Used
- Visual Studio Code: Source code editor with built-in terminal(s)
- GitHub: Popular hosting and code repository site (what you're likely viewing the code on)
- Cargo Rust Build Environment: Manages program and libraries for Rust

### Language & Libraries
- Rust: Programming Language used in application development, and embedded application development. It has improved memory mangement and variable ownership when compared to similar languages.
- __Libraries:__ *imported from the command line, "cargo add library_name"*
- io
- PathBuf
- Html
- Path
- csv
- reqwest
- scraper
- url

# Useful Websites
- [Rust Download](https://www.rust-lang.org/tools/install)
- [Rust Documentation & Setup Help](https://doc.rust-lang.org/book/index.html)
- [Rust Scraping Tutorial](https://www.zenrows.com/blog/rust-web-scraping#export-to-csv)
- [ChatGPT](https://chatgpt.com/) *Used for finding basic syntax, variable conversions, and scraper changes*

# Future Work

- **Scrape Dynamic Content**: The vast majority of today's websites use dynamically added content, which does not show up with this scraper. I did figure out a way to scrape dynamic content (it basically takes a screenshot of an active false browser). This was through headless chromium, but it kept crashing after spending over two hours debugging and tweaking.
- **Better Functions for Variable Passing**: It was quite frustrating at times, trying to pass variables from functions into the main program. Some lines (one converts a user-defined string into either a &str or a URL) could not passed from a function as they were pointers, and not their own variable. This addition would reduce code redundancy and the likilihood of large crashes.
- **Image Link Translations**: The image scraping option does save the provided page's image links, which is the functionality I intended it to have. I would like to also have the option to automatically download the actual images behind those links to a user-named subfolder, and possibly even ask to user to specify what image type the images should be saved as.
