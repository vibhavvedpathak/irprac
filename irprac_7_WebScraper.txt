import requests
from bs4 import BeautifulSoup
import time 
from urllib.parse import urljoin,urlparse
from urllib.robotparser import RobotFileParser

def get_html(url):
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}
    try:
        response = requests.get(url, headers=headers)
        response.raise_for_status()
        return response.text
    except requests.exceptions.HTTPError as errh:
        print(f'HTTP Error: {errh}')
    except requests.exceptions.RequestException as err:
        print(f'Request Error: {err}')
    return None

def save_robots_txt(url):
    try:
        robots_url = urljoin(url, '/robots.txt')
        robots_content = get_html(robots_url)
        if robots_content:
            with open('robots.txt', 'wb') as file:
                file.write(robots_content.encode('utf-8-sig'))
    except Exception as e:
        print(f'Error saving robots.txt: {e}')

def load_robots_txt():
    try:
        with open('robots.txt', 'rb') as file:
            return file.read().decode('utf-8-sig')
    except FileNotFoundError:
        return None

def extract_links(html, base_url):
    soup = BeautifulSoup(html, 'html.parser')
    links = []
    for link in soup.find_all('a', href=True):
        absolute_url = urljoin(base_url, link['href'])
        links.append(absolute_url)
    return links

def is_allowed_by_robots(url, robots_content):
    parser = RobotFileParser()
    parser.parse(robots_content.split('\n'))
    return parser.can_fetch('*', url)

def crawl(start_url, max_depth=3, delay=1):
    visited_urls = set()
    save_robots_txt(start_url)
    robots_content = load_robots_txt()
    if not robots_content:
        print('Unable to retrieve robots.txt. Crawling without restrictions')
    recursive_crawl(start_url, 1, robots_content, max_depth, visited_urls)

def recursive_crawl(url, depth, robots_content, max_depth, visited_urls):
    if depth > max_depth or url in visited_urls or not is_allowed_by_robots(url, robots_content):
        return
    visited_urls.add(url)
    html = get_html(url)
    if html:
        print(f"Crawling {url}")
        links = extract_links(html, url)
        for link in links:
            recursive_crawl(link, depth + 1, robots_content, max_depth, visited_urls)

# Example Usage
crawl('https://wikipedia.com', max_depth=2, delay=2)


