# How to Get an Answer to RP2040 in 4 Seconds with Groq

This project demonstrates using Groq's innovative LPU chip and Meta's LLaMA3 model to achieve fast response times with the RP2040 microcontroller, ideal for AIoT applications.

## Project Overview

Utilizing the cutting-edge technology from Groq and Meta, this project aims to replace traditional response mechanisms on the maker site with a much faster and efficient alternative using the RP2040 connected to Groq's cloud services.

## Hardware Components

- **WIZnet - W5100S-EVB-Pico x 1**

## Software and Libraries

- MicroPython
- Groq API


## Setup Instructions

1. **GroqCloud Setup**
   - Visit the [Groq Cloud homepage](https://api.groq.com/openai/v1/chat/completions).
   - Sign up and obtain your API KEY.
   - Ensure the Groq library is installed (not applicable directly on Pico, use API communication).

2. **Ethernet Initialization**
   - Use the following MicroPython code to initialize your Ethernet connection via WIZnet hardware:
   ```python
   from machine import Pin, SPI
   import network
   import utime

   def init_ethernet(timeout=10):
       spi = SPI(0, 2_000_000, mosi=Pin(19), miso=Pin(16), sck=Pin(18))
       nic = network.WIZNET5K(spi, Pin(17), Pin(20))
       nic.active(True)
       start_time = utime.ticks_ms()
       while not nic.isconnected():
           if (utime.ticks_diff(utime.ticks_ms(), start_time) > timeout * 1000):
               raise Exception("Ethernet connection timed out.")
           utime.sleep(1)
       print('Ethernet connected. IP:', nic.ifconfig())

## API Communication Example

- Here is how you can send a chat request to the Groq API and handle responses:
  ```python
  def send_chat_request(api_key, user_message):
      url = "https://api.groq.com/openai/v1/chat/completions"
      headers = {"Authorization": f"Bearer {api_key}", "Content-Type": "application/json"}
      payload = {"messages": [{"role": "user", "content": user_message}], "model": "llama3-70b-8192"}
      start_time = utime.ticks_ms()
      response = urequests.post(url, headers=headers, json=payload)
      elapsed_time = utime.ticks_diff(utime.ticks_ms(), start_time)
      if response.status_code == 200:
          try:
              content = response.json()['choices'][0]['message']['content']
              return content, elapsed_time
          except Exception as e:
              raise Exception("Failed to decode JSON from response: " + str(e))
      else:
          raise Exception(f"API error ({response.status_code}): {response.reason}")
  
## Running the Project

- Input your API key in the `main` function and initiate the chat sequence:
  ```python
  def main():
      api_key = 'your_api_key_here'
      init_ethernet()
      while True:
          user_input = input("User: ").strip()
          if user_input.lower() == "exit":
              print("Exiting...")
              break
          elif not user_input:
              continue
          try:
              response_content, time_taken = send_chat_request(api_key, user_input)
              print(f"LLaMA Response: {response_content} (Processed in {time_taken} ms)")
          except Exception as e:
              print("Error: ", e)

  if __name__ == "__main__":
      main()

## Result
![1](https://github.com/jh941213/Groq_W5100s_EVB_PICO/assets/112835087/ac246e96-483f-41dc-ab1a-a5195535688e)
![화면 기록 2024-04-22 오후 4 52 03](https://github.com/jh941213/Groq_W5100s_EVB_PICO/assets/112835087/6fcbde05-061f-4eac-b06e-3d41740a25b4)

