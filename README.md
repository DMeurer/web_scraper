# Web Scraping — only trough configuration

---

# How does this work?

The following is a flowchart of all decisions (white) and actions (green) performed by the program, and the configration options (blue) used in the process.
It should help to understand the workflow and configuration of this tool.

![Diagram](documentation_assets/WebScraper-configuration-diagram.svg)

The flowchart was created with [Excalidraw](https://excalidraw.com/), and can be found [here](documentation_assets/WebScraper-configuration-diagram.excalidraw).

Why is this an Excalidraw file export and not a mermaid diagram? — Because with Mermaid its mess.

# Found a bug?

Please report it to me, so I can fix it.
You can just create an issue on GitHub.

# Usage

## General

This does not require you to write any code. (Well a bit, depending on the way you see it).
This requires only configuration.

### How to configure?

There are many ways to configure this. By using the class methods one by one, by just loading a JSON file that contains all the configuration or command line arguments.

When you are using the prebuild `.exe` file, you have to use a JSON file or command line arguments.
Writing the settings JSON file by hand can be a bit tricky, so I recommend using the command line arguments.

#### Using a JSON file

When the program is started, it will print the configuration options currently applied as a JSON string.
This can be used to save the configuration for later use.

This Text can be stored in a `.json` file.

Later you can load this file with the `--config_file` argument.

Start the program with the following command:

```bash
scraper_no_ai.exe --config_file "path/to/your/config.json"
```

A configuration file might look like

```json
{
	"base_url": "https://www.surgicalholdings.co.uk/browse-products.html",
	"base_url_paging_prefix": "?p=",
	"base_url_paging_suffix": "",
	"pages_required": true,
	"page_start": 1,
	"page_step": 1,
	"page_end": 1000,
	"recursive_navigation": [
		{
			"css_selector": "ul>li>a",
			"base_url_in_case_links_are_relative": "https://www.surgicalholdings.co.uk"
		}
	],
	"product_element_css_selector": "div.m-product-item",
	"product_site_needed": true,
	"product_site_link_css_selector": "a.link",
	"product_site_link_prefix": "https://www.surgicalholdings.co.uk",
	"data_extraction": [
		{
			"column_name": "Name",
			"css_selector": ".product-name"
		},
		{
			"column_name": "ID",
			"css_selector": ".product-id"
		}
	],
	"verbose_output": false,
	"custom_http_headers": {
		"User-Agent": "Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148"
	},
	"url_blacklist_regex": ".*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(blog)|(account)|(browse-products)|(pdf-catalogue)|(about)|(contact)|(basket)).*",
	"output_file_path": "output.csv",
	"request_delay": 200
}
```

#### Using command line arguments

You can always use the `-h` or `--help` argument to get a list of all available arguments.

Available arguments are:

- `--base_url`: The base URL of the website you want to scrape.
- `--base_url_paging_prefix`: The prefix of the paging URL.
- `--base_url_paging_suffix`: The suffix of the paging URL.
- `--pages_required`: If the website has multiple pages, set this to `True`.
- `--page_start`: The starting page number.
- `--page_step`: The step between pages.
- `--page_end`: The ending page number.
- `--recursive_navigation`: A list of CSS selectors that will be used to navigate the website.
- `--product_element_css_selector`: The CSS selector of the product element.
- `--product_site_needed`: If the product site is needed, set this to `True`.
- `--product_site_link_css_selector`: The CSS selector of the product site link.
- `--product_site_link_prefix`: The prefix of the product site link.
- `--data_extraction`: A list of dictionaries with the keys `column_name` and `css_selector`.
- `--verbose_output`: If you want to see more output, set this to `True`.
- `--custom_http_headers`: Custom HTTP headers that will be sent with the request.
- `--url_blacklist_regex`: A regex pattern that will be used to filter out URLs.
- `--output_file_path`: The path to the output file.
- `--config_file`: The path to the configuration file.
- `--warning_tag_if_present`: This will create a column in the output file with the name `Warning` and will contain True if the tag is present and False if not.
- `--request_delay`: The delay between requests in seconds.

Example:

```bash
scraper_no_ai.exe --base_url "https://www.surgicalholdings.co.uk/browse-products.html" --pages_required True --base_url_paging_prefix "?p=" --page_start 1 --page_step 1 --page_end 1000 --recursive_navigation "[{\"css_selector\": \"ul>li>a\", \"base_url_in_case_links_are_relative\": \"https://www.surgicalholdings.co.uk\"}]" --product_element_css_selector "div.m-product-item" --product_site_needed True --product_site_link_css_selector "a.link" --product_site_link_prefix "https://www.surgicalholdings.co.uk" --data_extraction "[{\"column_name\": \"Name\", \"css_selector\": \".product-name\"}, {\"column_name\": \"ID\", \"css_selector\": \".product-id\"}]" --verbose_output False --custom_http_headers "{\"User-Agent\": \"Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148\"}" --url_blacklist_regex ".*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(blog)|(account)|(browse-products)|(pdf-catalogue)|(about)|(contact)|(basket)).*" --output_file_path "output.csv"
```

Step by step:

#### Using class methods (for Developers working directly with the code)

```python
# Import the class
from src.web_scraper_dmeurer import Settings

# Create an object of the Settings class
my_configuration = Settings()

# Set the base url. Your entry point.
my_configuration.set_base_url("https://www.surgicalholdings.co.uk/browse-products.html")
```

Now you can use this format for everything:

```python
my_configuration.set_pages_required(True)
my_configuration.set_base_url_paging_prefix("?p=")
my_configuration.set_page_range(1, 100, 1)
```

Or you can chain them:

```python
my_configuration.set_product_site_needed(True).set_product_site_link_css_selector("a.link").set_product_site_link_prefix("https://www.surgicalholdings.co.uk")
```

#### Using a JSON object (for Developers working directly with the code)

This can be usefull if you want to share your configuration with someone else, or if you want to save it for later use.

The JSON object can also be generated by the class methods and then calling the `get_settings()` method or str(my_configuration).

```python
# Import the class
from src.web_scraper_dmeurer import Settings

# Create an object of the Settings class
my_configuration = Settings()

# Load the JSON
settings_dict = {
    "base_url": "https://www.example.com",
    "base_url_paging_prefix": "/page/",
    "base_url_paging_suffix": "",
    "pages_required": True,
    "page_start": 1,
    "page_step": 1,
    "page_end": 10,
    "recursive_navigation": [{"css_selector": ".category"}],
    "product_element_css_selector": ".product",
    "product_site_needed": True,
    "product_site_link_css_selector": ".product-link",
    "product_site_link_prefix": "",
    "data_extraction": [{"column_name": "Name", "css_selector": ".product-name"}, {"column_name": "ID", "css_selector": ".product-id"}],
    "verbose_output": False,
    "custom_http_headers": {
        'User-Agent': 'Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148'
    },
    "url_blacklist_regex": ".*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(contact)).*",
    "output_file_path": "output.csv",
    "request_delay": 200
}
```

## How can I get the right CSS selectors?

There are plenty of guides on how to do this using the browser's developer tools.
Or you can contact Dominik Meurer and I will help you.

**Known Issue**

Mostly critical for category navigation, but this is valid for all CSS selectors.

When you have a website structure like this:

```html

<ul>
	<li>
		<a href="/some-category/">cat 1</a>
	</li>
	<li>
		<a href="/some-other-category/">cat 2</a>
	</li>
</ul>
```

You cant use the selector `ul>li>a` because it will only select the first match in the `ul` element.

--> So the only match will be `cat 1`.

To fix this, you have to edit your selector, so the first element does not contain multiple matches.

In this case, it would be `li>a`.

## Configuration

Options:
- `base_url`: The base URL of the website you want to scrape.
- `base_url_paging_prefix`: The prefix of the paging URL. For example, if the URL is `https://www.example.com/page/1`, the prefix is `/page/`.
- `base_url_paging_suffix`: The suffix of the paging URL. For example, if the URL is `https://www.example.com/page/1.html`, the suffix is `.html`.
- `pages_required`: If the website has multiple pages, set this to `True`.
- `page_start`: The starting page number.
- `page_step`: The step between pages.
- `page_end`: The ending page number. (In most cases, you can set this to an arbitrary large number if you don't know the number of pages, because it will stop when it doesn't get any results from a page.)
- `recursive_navigation`: A list of CSS selectors that will be used to navigate the website. For example, if you want to navigate through categories, you can set this to `[{ "css_selector": ".category" }]`. Each element in the list should be a dictionary with the key `css_selector` and optionally `base_url_in_case_links_are_relative` if relative links are used.
- `product_element_css_selector`: The CSS selector of the product element.
- `product_site_needed`: If the product site is needed, set this to `True`.
- `product_site_link_css_selector`: The CSS selector of the product site link.
- `product_site_link_prefix`: The prefix of the product site link. This is needed if the link is relative.
- `data_extraction`: A list of dictionaries with the keys `column_name` and `css_selector`. The `column_name` is the name of the column in the output CSV file, and the `css_selector` is the CSS selector of the element that contains the data.
- `verbose_output`: If you want to see more output, set this to `True`.
- `custom_http_headers`: Custom HTTP headers that will be sent with the request.
- `url_blacklist_regex`: A regex pattern that will be used to filter out URLs. If the URL matches the pattern, it will be ignored.
- `output_file_path`: The path to the output file.
- `config_file`: The path to the configuration file.
- `warning_tag_if_present`: This will create a column in the output file with the name `Warning` and will contain True if the tag is present and False if not.
- `request_delay`: The delay between requests in seconds.

I will explain the configuration option with the example of the website `https://www.surgicalholdings.co.uk`.

You will find code snippets for bash (top one) and python (below bash).

### base_url

The base URL is the starting point of the scraper. This is the URL that will be used to start the scraping process.

To make it easier to navigate, we try to minimize the navigation steps. So the starting point will be [https://www.surgicalholdings.co.uk/browse-products.html](https://www.surgicalholdings.co.uk/browse-products.html)

As JSON configuration:

```json
{
	"base_url": "https://www.surgicalholdings.co.uk/browse-products.html"
}
```

As a command line argument:
```bash
scraper_no_ai.exe --base_url "https://www.surgicalholdings.co.uk/browse-products.html"
```

As a python code:
```python
my_configuration.set_base_url("https://www.surgicalholdings.co.uk/browse-products.html")
```

### recursive_navigation

This is used to navigate through the website. 
In this example the `base_url` is an overview over categories.
So we need to navigate through the categories to get to the products.

In this case we can use the selector `li>a` to get all the link elements in the list.
This will also find all other links (to social media, login, etc.) so we need to filter them out later using the `url_blacklist_regex`.

> What is a CSS selector?
> Normally, you would use a CSS selector to style elements on a website. But in this case, we use it to find elements on the website.
> A guide on how to use CSS selectors can be found [here](https://www.w3schools.com/cssref/css_selectors.php) 
> (or in the [Official Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#css-selectors-through-the-css-property) of the used library). 
> Or you can always just google.

Since the links are relative, we need to set the `base_url_in_case_links_are_relative` to the base URL.

As JSON configuration:

```json
{
	"recursive_navigation": [
		{
			"css_selector": "ul>li>a",
			"base_url_in_case_links_are_relative": "https://www.surgicalholdings.co.uk"
		}
	]
}
```

As a command line argument:
```bash
scraper_no_ai.exe --recursive_navigation "[{\"css_selector\": \"ul>li>a\", \"base_url_in_case_links_are_relative\": \"https://www.surgicalholdings.co.uk\"}]"
```

As a python code:
```python
my_configuration.set_recursive_navigation(
    [
        {"css_selector": "ul>li>a", "base_url_in_case_links_are_relative": "https://www.surgicalholdings.co.uk"}
    ]
)
```

If we would want to add another navigation step, we could add another element to the list.

As JSON configuration:

```json
{
	"recursive_navigation": [
		{
			"css_selector": "ul>li>a",
			"base_url_in_case_links_are_relative": "https://www.surgicalholdings.co.uk"
		},
		{
			"css_selector": "ul>li>a",
			"base_url_in_case_links_are_relative": "https://www.surgicalholdings.co.uk"
		}
	]
}
```

As a command line argument:
```bash
scraper_no_ai.exe --recursive_navigation "[{\"css_selector\": \"ul>li>a\", \"base_url_in_case_links_are_relative\": \"https://www.surgicalholdings.co.uk\"}, {\"css_selector\": \"ul>li>a\", \"base_url_in_case_links_are_relative\": \"https://www.surgicalholdings.co.uk\"}]"
```

As a python code:
```python
my_configuration.set_recursive_navigation(
    [
        {"css_selector": "ul>li>a", "base_url_in_case_links_are_relative": "https://www.surgicalholdings.co.uk"},
        {"css_selector": "ul>li>a", "base_url_in_case_links_are_relative": "https://www.surgicalholdings.co.uk"}
    ]
)
```

### url_blacklist_regex

This is used to filter out URLs that we don't want to scrape. This is fully optional.

To find the right regex pattern, I highly recommend using [https://regexr.com/7u5v4](https://regexr.com/7u5v4) or something similar.
In the case of the website `https://www.surgicalholdings.co.uk`, we can use the following pattern: `.*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(blog)|(account)|(browse-products)|(pdf-catalogue)|(about)|(contact)|(basket)).*`

If any of the words in the brackets is in the URL, it will be skipped.

As JSON configuration:

```json
{
	"url_blacklist_regex": ".*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(blog)|(account)|(browse-products)|(pdf-catalogue)|(about)|(contact)|(basket)).*"
}
```

As a command line argument:
```bash
scraper_no_ai.exe --url_blacklist_regex ".*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(blog)|(account)|(browse-products)|(pdf-catalogue)|(about)|(contact)|(basket)).*"
```

As a python code:
```python
my_configuration.set_url_blacklist_regex(".*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(blog)|(account)|(browse-products)|(pdf-catalogue)|(about)|(contact)|(basket)).*")
```

### pages

If your catalog page, you want to load has multiple pages, you can set the following options.

You need to enable the `pages_required` option and set the `page_start`, `page_step` and `page_end`.
This will activate the system and go from start to end with the step size.

The `page_end` can be set to an arbitrary large number if you don't know the number of pages, because it will stop when it doesn't get any results from a page.

Additionally, in most cases, you need to set the `base_url_paging_prefix` and `base_url_paging_suffix`.

The prefix is the part between the URL and the page number.
In the case of the website `https://www.surgicalholdings.co.uk`, the prefix is `?p=`, but it can also be `/page/` or something else.

The suffix is the part after the page number.
In the case of the website `https://www.surgicalholdings.co.uk`, the suffix is ``, because we don't need it, but it can also be `.html` or something else.

As JSON configuration:

```json
{
	"pages_required": true,
	"page_start": 1,
	"page_step": 1,
	"page_end": 1000,
	"base_url_paging_prefix": "?p=",
	"base_url_paging_suffix": ""
}
```

As a command line argument:
```bash
scraper_no_ai.exe --pages_required True --base_url_paging_prefix "?p=" --page_start 1 --page_step 1 --page_end 1000
```

As a python code:
```python
my_configuration.set_pages_required(True)
my_configuration.set_base_url_paging_prefix("?p=")
my_configuration.set_page_range(1, 1000, 1)
```

### product_element_css_selector

Now we are on the gallery page of the products. We need to find the CSS selector of the product element, to continue.

In the case of the website `https://www.surgicalholdings.co.uk`, the selector is `div.m-product-item`.

As JSON configuration:

```json
{
	"product_element_css_selector": "div.m-product-item"
}
```

As a command line argument:
```bash
scraper_no_ai.exe --product_element_css_selector "div.m-product-item"
```

As a python code:
```python
my_configuration.set_product_element_css_selector("div.m-product-item")
```

### product_site_needed

Before we can extract the data, we need to think about if the gallery has all the information we need, or if we need to go to the product site.

In this example, it is necessary because the gallery only contains the name and the ID can only be found on the product page.

To make it work, we need an CSS selector for the link to the product site, and a base URL in case the link is relative.

As JSON configuration:

```json
{
	"product_site_needed": true,
	"product_site_link_css_selector": "a.link",
	"product_site_link_prefix": "https://www.surgicalholdings.co.uk"
}
```

As a command line argument:
```bash
scraper_no_ai.exe --product_site_needed True --product_site_link_css_selector "a.link" --product_site_link_prefix "https://www.surgicalholdings.co.uk"
```

As a python code:
```python
my_configuration.set_product_site_needed(True)
my_configuration.set_product_site_link_css_selector("a.link")
my_configuration.set_product_site_link_prefix("https://www.surgicalholdings.co.uk")
```

### data_extraction

Now we can extract the data from the site. Whether its the gallery or the product site, as long as all the data is on the same site.

In this example, we want to extract the name and the ID of the product.

The configuration is in the JSON format. The `column_name` is the name of the column in the output CSV file, and the `css_selector` is the CSS selector of the element that contains the data.

As JSON configuration:

```json
{
	"data_extraction": [
		{
			"column_name": "Name",
			"css_selector": ".product-name"
		},
		{
			"column_name": "ID",
			"css_selector": ".product-id"
		}
	]
}
```

As a command line argument:
```bash
scraper_no_ai.exe --data_extraction "[{\"column_name\": \"Name\", \"css_selector\": \".product-name\"}, {\"column_name\": \"ID\", \"css_selector\": \".product-id\"}]"
```

As a python code:
```python
my_configuration.set_data_extraction(
    [
        {"column_name": "Name", "css_selector": ".product-name"},
        {"column_name": "ID", "css_selector": ".product-id"}
    ]
)
```

With this pattern you can add as many columns as you want.

### verbose_output

If you want to see more output, you can set this to `True`.

As JSON configuration:

```json
{
	"verbose_output": true
}
```

As a command line argument:
```bash
scraper_no_ai.exe --verbose_output True
```

As a python code:
```python
my_configuration.set_verbose_output(True)
```

### custom_http_headers

If you need to send custom HTTP headers with the request, you can set this option.
By default, the scraper sends the following headers:

```json
{
	"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3"
}
```

You can set them on your own.

As JSON configuration:

```json
{
	"custom_http_headers": {
		"User-Agent": "Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148"
	}
}
```

As a command line argument:
```bash
scraper_no_ai.exe --custom_http_headers "{\"User-Agent\": \"Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148\"}"
```

As a python code:
```python
my_configuration.set_custom_http_headers({
    'User-Agent': 'Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148'
})
```

### output_file_path

The path to the output file.

As JSON configuration:

```json
{
	"output_file_path": "output.csv"
}
```

As a command line argument:
```bash
scraper_no_ai.exe --output_file_path "output.csv"
```

As a python code:
```python
my_configuration.set_output_file_path("output.csv")
```

### warning_tag_if_present

This will create a column in the output file with the name `Warning` and will contain True if the tag is present and False if not.

As JSON configuration:

```json
{
	"warning_tag_if_present": ".element-i-dont-want-to-see"
}
```

As a command line argument:
```bash
scraper_no_ai.exe --warning_tag_if_present ".element-i-dont-want-to-see"
```

As a python code:
```python
my_configuration.set_warning_tag_if_present(".element-i-dont-want-to-see")
```

### request_delay

The delay between requests in seconds.

This is useful if you don't want to overload the server with requests. Sending too many requests in a short period of time can lead to your IP address being blocked.

As JSON configuration:

```json
{
	"request_delay": 1
}
```

As a command line argument:

```bash
scraper_no_ai.exe --request_delay 1
```

As a python code:

```python
my_configuration.set_request_delay(1)
```

## Start the scraper

Now you can start the scraper.

### Using the prebuild `.exe` file

Using the JSON file, run:

```bash
sceaper_no_ai.exe --config_file "path/to/your/config.json"
```

Using the command line arguments, run:
```bash
scraper_no_ai.exe --base_url "https://www.surgicalholdings.co.uk/browse-products.html" --pages_required True --base_url_paging_prefix "?p=" --page_start 1 --page_step 1 --page_end 1000 --recursive_navigation "[{\"css_selector\": \"ul>li>a\", \"base_url_in_case_links_are_relative\": \"https://www.surgicalholdings.co.uk\"}]" --product_element_css_selector "div.m-product-item" --product_site_needed True --product_site_link_css_selector "a.link" --product_site_link_prefix "https://www.surgicalholdings.co.uk" --data_extraction "[{\"column_name\": \"Name\", \"css_selector\": \".product-name\"}, {\"column_name\": \"ID\", \"css_selector\": \".product-id\"}]" --verbose_output False --custom_http_headers "{\"User-Agent\": \"Mozilla/5.0 (iPad; CPU OS 12_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148\"}" --url_blacklist_regex ".*((instagram)|(facebook)|(twitter)|(linkedin)|(youtube)|(login)|(spotify)|(blog)|(account)|(browse-products)|(pdf-catalogue)|(about)|(contact)|(basket)).*" --output_file_path "output.csv"
```

### Using the class methods

Insert your configuration calls into `main.py`:

```python
import sys
from src.web_scraper_dmeurer import Scraper

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Please provide a configuration file path as the first argument.")
        sys.exit(1)

    scraper = Scraper()

    # Your configuration here

    # Your configuration above

    scraper.start_scraper(run_args=sys.argv[1:])

    sys.exit(0)
```

And run the script without any arguments:

```bash
python main.py
```

# Hint for developing this

Compile and push using:

```PowerShell
py -m build
py -m twine upload --repository pypi dist/*
```
