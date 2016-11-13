#include <SPI.h>
#include <Ethernet.h>

// Enter a MAC address and IP address for your controller below.
// The IP address will be dependent on your local network:
byte mac[] = {
  0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED
};
IPAddress ip(192, 168, 1, 177);

// Initialize the Ethernet server library
// with the IP address and port you want to use
// (port 80 is default for HTTP):
EthernetServer server(80);

// constants won't change. They're used here to
// set pin numbers:
const int door_switch_pin = 5;     // the number of the door switch pin
const int button_pin = 6;         // the number of the device button pin
const int relay_pin = 7;         //the number of the relay control pin

void setup() {
  // initialize the relay control pin as an output:
  pinMode(relay_pin, OUTPUT);
  // relau is activated by high state
  digitalWrite(relay_pin, LOW);
  // initialize the pushbutton pin as an input:
  pinMode(button_pin, INPUT);
  // initialize the door switch  pin as an input:
  pinMode(door_switch_pin, INPUT);  
  
  // Open serial communications and wait for port to open:
  Serial.begin(9600);
  while (!Serial) {
    ; // wait for serial port to connect. Needed for native USB port only
  }


  // start the Ethernet connection and the server:
  Ethernet.begin(mac, ip);
  server.begin();
  Serial.print("server is at ");
  Serial.println(Ethernet.localIP());
}


void loop() {
  // listen for incoming clients
  EthernetClient client = server.available();
  if (client) {
    Serial.println("new client");
    // an http request ends with a blank line
    boolean currentLineIsBlank = true;
    while (client.connected()) {
      if (client.available()) {
        char c = client.read();
        Serial.write(c);
        // if you've gotten to the end of the line (received a newline
        // character) and the line is blank, the http request has ended,
        // so you can send a reply
        if (c == '\n' && currentLineIsBlank) {
          // send a standard http response header
          client.println("HTTP/1.1 200 OK");
          client.println("Content-Type: text/html");
          client.println("Connection: close");  // the connection will be closed after completion of the response
          client.println("Refresh: 5");  // refresh the page automatically every 5 sec
          client.println();
          client.println("<!DOCTYPE HTML>");
          client.println("<html>");
          client.print("Door is ");
          // check if the door switch is pressed (LOW signal).
          if(digitalRead(door_switch_pin)== LOW){
            client.println("closed");
          }else{
            client.println("open");
          }
          client.println("<br />");
          //check the load state
          client.print("Load is switched ");
           if(analogRead(1)<=1015) {
            client.print("ON");
           }else{
                client.print("OFF");
                }
            client.println("<br />");
            //draw a switch button
            client.println("<a href=\"/on\"><button>ON</button></a>");
            client.println("<a href=\"/off\"><button>OFF</button></a>");
            client.println("<br />");
          client.println("</html>");
          break;
        }
        if (c == '\n') {
          // you're starting a new line
          currentLineIsBlank = true;
        } else if (c != '\r') {
          // you've gotten a character on the current line
          currentLineIsBlank = false;
        }
      }
    }
    // give the web browser time to receive the data
    delay(1);
    // close the connection:
    client.stop();
    Serial.println("client disconnected");
  }
}

