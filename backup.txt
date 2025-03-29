use std::io;
use std::path::PathBuf;
use scraper::Html;
use std::path::Path;
//use url::Url;

//terminal code used:
//cargo new fallout_site_scraper
//--created a new project (in this case, fallout_site_scraper)
//--along with the Cargo.toml file and src directory (holds the project)
//cargo build
//--compiles the app, required every time, unless run is flexible (typicall is)
//cargo run
//--runs the app
//cargo add scraper reqwest --features "reqwest/blocking"
//--adds the reqwest and scaper libraries, reqwest and scraper
//---scraper: parser API that travels and manipulates HTML docs
//---reqwest: HTTP client enabling web requests in Rust, (uses proxies, cookies, custom headers)
//cargo add csv
//--download the csv library
//cargo add url
//--adds the cargo url library for URL manipulation

//asks the user to name their file, and then exports the directory path for it.
fn name_your_file() -> PathBuf{
    //create a blank new string
    let mut temp_string = String::new();

    //ask the user
    println!("\nEnter your csv file name: ");

    //read from the line, assign to temp_string
    io::stdin().read_line(&mut temp_string).expect("Failed to read input");

    //create a raw version of the directory path
    let raw_path = format!("{}.csv", temp_string.trim());

    //converts the path into purified water
    //also creates the file if it doesn't exist
    let my_path: PathBuf = PathBuf::from(raw_path);

    PathBuf::from(my_path)
}

//takes a string(URL input), converts it into html content
fn reqwest_breakdown(sp_html: &str) -> Html{
    //request the html content
    let response = reqwest::blocking::get(sp_html);

    //convert to text?
    let html_content = response.unwrap().text().unwrap();

    //use the scraper
    let document = scraper::Html::parse_document(&html_content);

    document
}

fn take_url() -> String{
    //declare new temperoray string
    let mut temp_string = String::new();

    //ask the user to name it
    println!("\nEnter your website's URL:  ");
    println!("Left-Click to paste a copied line into the terminal entry");
    io::stdin().read_line(&mut temp_string).expect("Failed to read input");

    //I tried to convert the read string into a &str element, as that's what reqwest breakdown requires, it would not work, so that 
    //conversion line is present in each menu item   (I think it's something to do with referencing a variable in/outside of it's
    //function's scope?)
    //let short_string = &str::parse(&temp_string).unwrap();
    //let short_string = temp_string.as_str();

    //return the temp string
    temp_string
}

fn main() {
    //init the loop status
    let mut loop_status = true;

    //print welcome msg
    println!("\nWelcome to the RUST SCRAPER!");
    println!("Each of the provided options will scrape the specified content \nfrom a URL, which you provide!");

    //start up the menu loop
    while loop_status == true {
    
        //let mut file_path = PathBuf::new();
        let mut read_string = String::new();

        println!("\nWhat do you want to scrape?");
        println!("1. Scrape all the content of all <p> (paragraph) tags.\n2. Scrape <a href> tags or external links (NOT images)\n3. Scrape all <script> links and manually embedded scripts\n4. Scrape Image Links\n5. Quit Program\n");
        
        io::stdin().read_line(&mut read_string).expect("Failed to read input");
        let user_number: i32 = read_string.trim().parse().expect("Invalid Number");
        match user_number{
            //scrape <p>
            1 => {
                //retrieve file name and path
                let file_path = name_your_file();
                //assign a cloned version of the path to a variable for later use
                let temp_path = file_path.clone();

                //retrieves the scraped/formatted document (not quite sure)
                let user_url = take_url();

                //converts the url from a string into a &str
                let off_url: &str = &user_url;

                //CODE DIFFER!!
                println!("\nScraping <p> Tags...\n");

                //calls reqwest breakdown for the url
                let temp_full_html = reqwest_breakdown(off_url);

                //write out what the scraper will select
                let item_selector = scraper::Selector::parse("p").unwrap();

                //open a new writer from the original source
                let mut my_writer = csv::Writer::from_path(file_path).unwrap();

                //apply the selector and, convert to strings, and write to the specified file
                for p_ele in temp_full_html.select(&item_selector) {
                    let text_content: String = p_ele.text().collect(); 
                    my_writer.write_record(&[text_content]).expect("Failed to write record");
                }

                //directly print the cloned file path, does NOT work with printing the original path
                println!("\nScraping Successful! See results in {}\n", temp_path.display());
            }
            //scrape a href
            2 => {
                //retrieve file name and path
                let file_path = name_your_file();
                //assign a cloned version of the path to a variable for later use
                let temp_path = file_path.clone();

                //retrieves the scraped/formatted document (not quite sure)
                //let temp_full_html = reqwest_breakdown("https://fallout.bethesda.net/en");
                let user_url = take_url();

                //converts the url from a string into a &str
                let off_url: &str = &user_url;

                println!("\nScraping a -> href Links...\n");

                //calls reqwest breakdown for the url
                let temp_full_html = reqwest_breakdown(off_url);

                //write out what the scraper will select
                let item_selector = scraper::Selector::parse("a").unwrap();

                //open a new writer from the original source
                let mut my_writer = csv::Writer::from_path(file_path).unwrap();

                //for each selected element, extract the src url and write it to our file
                for temp_element in temp_full_html.select(&item_selector) {
                    if let Some(link_url) = temp_element.value().attr("href") {
                        my_writer.write_record(&[link_url]).unwrap();
                    }
                }

                //directly print the cloned file path, does NOT work with printing the original path
                println!("\nScraping Successful! See results in {}\n", temp_path.display());

            }
            //scrape scripts
            3 => {
                //retrieve file name and path
                let file_path = name_your_file();
                //assign a cloned version of the path to a variable for later use
                let temp_path = file_path.clone();

                //retrieves the scraped/formatted document (not quite sure)
                let user_url = take_url();

                //converts the url from a string into a &str
                let off_url: &str = &user_url;

                println!("\nScraping <script> Tags...\n");

                //calls reqwest breakdown for the url
                let temp_full_html = reqwest_breakdown(off_url);

                //write out what the scraper will select
                let item_selector = scraper::Selector::parse("script").unwrap();

                //open a new writer from the original source
                let mut my_writer = csv::Writer::from_path(file_path).unwrap();

                //apply the selector and, convert to strings, and write to the specified file
                for script_ele in temp_full_html.select(&item_selector) {
                    let text_content: String = script_ele.text().collect(); 
                    my_writer.write_record(&[text_content]).expect("Failed to write record");
                }

                //directly print the cloned file path, does NOT work with printing the original path
                println!("\nScraping Successful! See results in {}\n", temp_path.display());
                println!("Additonal Note: Script Scraping may yield massive text blobs for extremely\ndynamic/personalized websites, or just pointers to a server's file...")
            }
            //scrape img links
            4 => {
                //retrieve file name and path
                let file_path = name_your_file();
                //assign a cloned version of the path to a variable for later use
                let temp_path = file_path.clone();

                //retrieves the scraped/formatted document (not quite sure)
                //let temp_full_html = reqwest_breakdown("https://fallout.bethesda.net/en");
                let user_url = take_url();

                //converts the url from a string into a &str
                let off_url: &str = &user_url;

                println!("\nScraping Image Links...\n");

                //calls reqwest breakdown for the url
                let temp_full_html = reqwest_breakdown(off_url);

                //write out what the scraper will select
                let item_selector = scraper::Selector::parse("img").unwrap();

                //open a new writer from the original source
                let mut my_writer = csv::Writer::from_path(file_path).unwrap();

                //for each selected element, extract the src url and write it to our file
                for temp_element in temp_full_html.select(&item_selector) {
                    if let Some(img_url) = temp_element.value().attr("src") {
                        my_writer.write_record(&[img_url]).unwrap();
                    }
                }

                //directly print the cloned file path, does NOT work with printing the original path
                println!("\nScraping Successful! See results in {}\n", temp_path.display());

    //backup code
                // //retrieves the scraped/formatted document (not quite sure)
                // let temp_full_html = reqwest_breakdown("https://fallout.bethesda.net/en");
                
                // //write out what the scraper will select
                // let item_selector = scraper::Selector::parse("img").unwrap();

                // let test_list = temp_full_html.select(&item_selector);


                // let mut my_writer = csv::Writer::from_path(file_path).unwrap();

                // for article_element in test_list {
                //     let temp_text = article_element.text().collect::<String>(); // Extract text
                //     my_writer.write_record(&[temp_text]).unwrap(); // Write to CSV
                // }

                // my_writer.flush().unwrap();
            }
            //quit prog
            5 => {
                loop_status = false; 
                println!("\nQuitting Program, Goodbye!\n");
            }
            _ => println!("\nPlease input a valid option.\n"),
        }
    
    }

     
}





