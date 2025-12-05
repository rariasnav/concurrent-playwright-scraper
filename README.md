🚀 Build a Concurrent & Scalable Web Scraper with Playwright, Python, MySQL, Docker, and Kubernetes

Leverage the power of asynchronous Python, containerization, and Kubernetes orchestration to scrape data efficiently and scale seamlessly.

1️⃣ Concurrent Scraping with Playwright & Python

Playwright enables asynchronous web interactions, allowing multiple pages to be scraped concurrently.

Installation:

pip install -r requirements.txt


Example Async Scraper:

import asyncio
from playwright.async_api import async_playwright

async def scrape_page(url):
    async with async_playwright() as p:
        browser = await p.chromium.launch()
        page = await browser.new_page()
        await page.goto(url)
        name = await page.locator('h1').text_content()
        price = await page.locator('.price_color').text_content()
        upc = await page.locator('//th[text()="UPC"]/following-sibling::td').text_content()
        await browser.close()
        return {"name": name, "price": price, "upc": upc}

async def main(urls):
    tasks = [scrape_page(url) for url in urls]
    results = await asyncio.gather(*tasks)
    print(results)

urls = ["https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html"]
asyncio.run(main(urls))


✅ Benefits: Concurrent requests reduce total scraping time significantly.

2️⃣ Data Storage with MySQL

Store scraped data in MySQL for reliable storage and easy querying.

Start Minikube & Deploy MySQL:

minikube start --cpus=6 --memory=8192
kubectl apply -f mysql-secret.yaml
kubectl apply -f mysql-storage.yaml
kubectl apply -f mysql-deployment.yaml
kubectl get deployments


Access MySQL Pod:

kubectl exec -it <mysql_pod_name> -- /bin/bash
mysql -u root -p


Create Database & Table:

CREATE DATABASE k8;
USE k8;

CREATE TABLE IF NOT EXISTS books (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    price VARCHAR(50),
    upc VARCHAR(20)
);


💡 Tip: Use bulk inserts for high-volume scraping.

3️⃣ Containerization with Docker

Package your scraper and dependencies for consistent deployments.

Docker Pull Example:

docker pull aakash22mahawar/books_k8:latest


Dockerfile Example:

FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "scraper.py"]


✅ Benefits: Ensures same environment across dev, staging, and production.

4️⃣ Scalability with Kubernetes

Deploy your scraper to Kubernetes for automatic scaling and robust orchestration.

Deploy Scraper:

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get pods
kubectl logs -f <pod_name>


Optional: Open Kubernetes Dashboard

kubectl proxy
# Then visit: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/


💡 Tip: Use Horizontal Pod Autoscaler (HPA) to scale scraper pods based on CPU or memory usage:

kubectl autoscale deployment scraper-deployment --cpu-percent=70 --min=2 --max=10
