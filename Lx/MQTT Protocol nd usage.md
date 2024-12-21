
## ==Claud==

I'll help you create a Python script that demonstrates MQTT communication on a Raspberry Pi, including both publishing and subscribing to topics.



```python
import paho.mqtt.client as mqtt
import json
import time
from datetime import datetime
import RPi.GPIO as GPIO

class RaspberryMQTT:
    def __init__(self, broker="localhost", port=1883, client_id="raspberry_pi"):
        """
        Initialize MQTT client for Raspberry Pi
        
        Args:
            broker (str): MQTT broker address
            port (int): MQTT broker port
            client_id (str): Unique client identifier
        """
        self.broker = broker
        self.port = port
        self.client = mqtt.Client(client_id)
        
        # Set up callbacks
        self.client.on_connect = self.on_connect
        self.client.on_message = self.on_message
        self.client.on_disconnect = self.on_disconnect
        
        # Initialize GPIO
        GPIO.setmode(GPIO.BCM)
        self.led_pin = 18
        GPIO.setup(self.led_pin, GPIO.OUT)
        
    def on_connect(self, client, userdata, flags, rc):
        """Callback for when client connects to broker"""
        connection_status = {
            0: "Successfully connected",
            1: "Incorrect protocol version",
            2: "Invalid client ID",
            3: "Server unavailable",
            4: "Invalid username/password",
            5: "Not authorized"
        }
        print(f"Connection status: {connection_status.get(rc, 'Unknown error')}")
        
        if rc == 0:
            print("Connected to MQTT broker")
            # Subscribe to topics
            self.client.subscribe([
                ("raspberry/sensors/#", 1),  # All sensor data
                ("raspberry/commands/#", 1),  # All commands
                ("raspberry/status", 1)       # Status messages
            ])
    
    def on_message(self, client, userdata, msg):
        """Callback for when a message is received"""
        try:
            payload = msg.payload.decode()
            print(f"Received message on topic {msg.topic}: {payload}")
            
            # Handle different topics
            if msg.topic == "raspberry/commands/led":
                self.handle_led_command(payload)
            elif msg.topic == "raspberry/commands/status":
                self.publish_status()
                
        except Exception as e:
            print(f"Error processing message: {e}")
    
    def on_disconnect(self, client, userdata, rc):
        """Callback for when client disconnects from broker"""
        print("Disconnected from MQTT broker")
        if rc != 0:
            print("Unexpected disconnection. Attempting to reconnect...")
            self.connect()
    
    def connect(self):
        """Connect to MQTT broker"""
        try:
            self.client.connect(self.broker, self.port, keepalive=60)
            self.client.loop_start()
        except Exception as e:
            print(f"Error connecting to broker: {e}")
    
    def disconnect(self):
        """Disconnect from MQTT broker"""
        self.client.loop_stop()
        self.client.disconnect()
        GPIO.cleanup()
    
    def publish_sensor_data(self, sensor_type, data):
        """
        Publish sensor data to MQTT broker
        
        Args:
            sensor_type (str): Type of sensor (e.g., 'temperature', 'humidity')
            data: Sensor reading
        """
        message = {
            "timestamp": datetime.now().isoformat(),
            "value": data,
            "unit": self.get_unit(sensor_type)
        }
        
        topic = f"raspberry/sensors/{sensor_type}"
        self.client.publish(topic, json.dumps(message), qos=1)
        print(f"Published to {topic}: {message}")
    
    def publish_status(self):
        """Publish Raspberry Pi status"""
        status = {
            "timestamp": datetime.now().isoformat(),
            "device_id": self.client._client_id.decode(),
            "status": "online",
            "ip_address": self.get_ip_address()
        }
        
        self.client.publish("raspberry/status", json.dumps(status), qos=1, retain=True)
    
    def handle_led_command(self, payload):
        """Handle LED control commands"""
        try:
            command = json.loads(payload)
            if 'state' in command:
                GPIO.output(self.led_pin, command['state'])
                print(f"LED set to {command['state']}")
        except json.JSONDecodeError:
            print("Invalid LED command format")
    
    @staticmethod
    def get_unit(sensor_type):
        """Get unit for sensor type"""
        units = {
            'temperature': '°C',
            'humidity': '%',
            'pressure': 'hPa',
            'light': 'lux'
        }
        return units.get(sensor_type, 'unknown')
    
    @staticmethod
    def get_ip_address():
        """Get Raspberry Pi IP address"""
        try:
            import socket
            s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
            s.connect(("8.8.8.8", 80))
            ip = s.getsockname()[0]
            s.close()
            return ip
        except Exception:
            return "unknown"

def main():
    # Example usage
    try:
        # Create MQTT client
        mqtt_client = RaspberryMQTT(
            broker="test.mosquitto.org",  # Public test broker
            port=1883,
            client_id="raspberry_pi_test"
        )
        
        # Connect to broker
        mqtt_client.connect()
        print("Connected to MQTT broker. Press Ctrl+C to exit.")
        
        # Publish initial status
        mqtt_client.publish_status()
        
        # Main loop
        while True:
            # Simulate sensor readings
            temperature = 23.5
            humidity = 45.2
            
            # Publish sensor data
            mqtt_client.publish_sensor_data("temperature", temperature)
            mqtt_client.publish_sensor_data("humidity", humidity)
            
            time.sleep(5)  # Wait 5 seconds between readings
            
    except KeyboardInterrupt:
        print("\nExiting...")
    finally:
        mqtt_client.disconnect()

if __name__ == "__main__":
    main()

```

To use this script, you'll first need to install the MQTT client library:
```bash
pip3 install paho-mqtt
```

This script provides:

1. MQTT Client Setup:
   - Connects to an MQTT broker
   - Handles connections, disconnections, and messages
   - Supports QoS levels and retained messages

2. Publishing Features:
   - Sensor data publishing with timestamps
   - Status updates
   - Formatted JSON payloads

3. Subscription Features:
   - Topic subscriptions with wildcards
   - Command handling (e.g., LED control)
   - Status request handling

4. Error Handling:
   - Connection error handling
   - Message processing error handling
   - Automatic reconnection

To test the script:

1. Start a local MQTT broker (if not using a public one):
```bash
sudo apt-get install mosquitto mosquitto-clients
sudo systemctl start mosquitto
```

2. Monitor messages using mosquitto client:
```bash
mosquitto_sub -h localhost -t "raspberry/#" -v
```

3. Send commands:
```bash
mosquitto_pub -h localhost -t "raspberry/commands/led" -m '{"state": true}'
```

Would you like me to:
- Add authentication support?
- Include more sensor types?
- Add SSL/TLS security?
- Implement specific message patterns?
- Add data persistence?



## ==Claud==

To set up MQTT (Message Queuing Telemetry Transport) protocol on a Raspberry Pi and enable communication using Python, follow these steps:

---

### **1. Install the MQTT Broker**

The MQTT broker is the central hub for message exchange. On a Raspberry Pi, you can use **Mosquitto**.

#### Install Mosquitto:

```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```

#### Test the Broker (Optional):

- Open two terminal windows.
- In the first terminal, start a subscriber:
    
    ```bash
    mosquitto_sub -h localhost -t test/topic
    ```
    
- In the second terminal, publish a message:
    
    ```bash
    mosquitto_pub -h localhost -t test/topic -m "Hello MQTT"
    ```
    

If the subscriber receives the message, the broker is working correctly.

---

### **2. Install the Python MQTT Library**

Install the **paho-mqtt** library, a popular Python client for MQTT communication:

```bash
pip install paho-mqtt
```

---

### **3. Write MQTT Communication Scripts**

#### MQTT Publisher Script

This script publishes messages to a specific topic.

```python
import paho.mqtt.client as mqtt

BROKER = "localhost"  # Use the broker's IP address or hostname
PORT = 1883           # Default MQTT port
TOPIC = "test/topic"

# Create MQTT client instance
client = mqtt.Client()

# Connect to the broker
client.connect(BROKER, PORT, 60)

# Publish messages
try:
    while True:
        message = input("Enter message to publish: ")
        client.publish(TOPIC, message)
        print(f"Message '{message}' sent to topic '{TOPIC}'")
except KeyboardInterrupt:
    print("Exiting...")
finally:
    client.disconnect()
```

---

#### MQTT Subscriber Script

This script subscribes to a topic and listens for messages.

```python
import paho.mqtt.client as mqtt

BROKER = "localhost"  # Use the broker's IP address or hostname
PORT = 1883           # Default MQTT port
TOPIC = "test/topic"

# Callback function to handle incoming messages
def on_message(client, userdata, msg):
    print(f"Received message: {msg.payload.decode()} on topic {msg.topic}")

# Create MQTT client instance
client = mqtt.Client()

# Set callback for messages
client.on_message = on_message

# Connect to the broker
client.connect(BROKER, PORT, 60)

# Subscribe to the topic
client.subscribe(TOPIC)

# Start the MQTT loop to process messages
try:
    print(f"Subscribed to topic '{TOPIC}'. Waiting for messages...")
    client.loop_forever()
except KeyboardInterrupt:
    print("Exiting...")
finally:
    client.disconnect()
```

---

### **4. Test the MQTT Setup**

1. Run the **subscriber script** in one terminal:
    
    ```bash
    python3 mqtt_subscriber.py
    ```
    
2. Run the **publisher script** in another terminal:
    
    ```bash
    python3 mqtt_publisher.py
    ```
    
3. Send messages using the publisher and verify they are received by the subscriber.

---

### **5. Advanced MQTT Features**

#### a. Publish Sensor Data

Modify the publisher to send sensor readings (e.g., from a GPIO pin):

```python
import RPi.GPIO as GPIO
import paho.mqtt.client as mqtt
import time

GPIO.setmode(GPIO.BCM)
SENSOR_PIN = 4
GPIO.setup(SENSOR_PIN, GPIO.IN)

BROKER = "localhost"
PORT = 1883
TOPIC = "sensor/data"

client = mqtt.Client()
client.connect(BROKER, PORT, 60)

try:
    while True:
        sensor_value = GPIO.input(SENSOR_PIN)
        client.publish(TOPIC, f"Sensor Value: {sensor_value}")
        print(f"Published: Sensor Value: {sensor_value}")
        time.sleep(1)
except KeyboardInterrupt:
    print("Exiting...")
finally:
    GPIO.cleanup()
    client.disconnect()
```

#### b. Control Devices Using MQTT

Modify the subscriber to control an LED based on received messages:

```python
import RPi.GPIO as GPIO
import paho.mqtt.client as mqtt

GPIO.setmode(GPIO.BCM)
LED_PIN = 18
GPIO.setup(LED_PIN, GPIO.OUT)

BROKER = "localhost"
PORT = 1883
TOPIC = "led/control"

def on_message(client, userdata, msg):
    message = msg.payload.decode()
    if message == "ON":
        GPIO.output(LED_PIN, GPIO.HIGH)
        print("LED turned ON")
    elif message == "OFF":
        GPIO.output(LED_PIN, GPIO.LOW)
        print("LED turned OFF")

client = mqtt.Client()
client.on_message = on_message
client.connect(BROKER, PORT, 60)
client.subscribe(TOPIC)

try:
    print(f"Subscribed to topic '{TOPIC}'. Waiting for commands...")
    client.loop_forever()
except KeyboardInterrupt:
    print("Exiting...")
finally:
    GPIO.cleanup()
    client.disconnect()
```

---

### **6. Notes**

- **Remote Broker**: Replace `localhost` with the broker’s IP or hostname to communicate with remote devices.
- **Security**:
    - Use **TLS/SSL** for encrypted communication.
    - Implement username/password authentication.
- **QoS Levels**: Adjust the Quality of Service (QoS) levels in `client.subscribe(TOPIC, qos=1)` for reliability.

Let me know if you need more help with MQTT or specific use cases!