# Derailment-inspection-monitoring-bot#include <hcrs04.h>
#define SSID        "friends"
        #define PASS        "walchand"
        #define DST_IP      "things.ubidots.com"
        #define idvariable  "A1E-20a8261d1c0c618c857aad46025ead934967"   // replace with your Ubidots Variable ID
        #define token       "A1E-vkAnZskWfspYgLlO8U9HlUkH0YonEi"  // replace with your Ubidots token
        int len;
#define PINTRIG 18
#define PINECHO 17

hcrs04 mySensor(PINTRIG, PINECHO);
 
void setup()
{
  Serial.begin(9600); 
  Serial.println("HUU YAY ULTRASONIC");
  mySensor.begin(); 
  // Open serial communications and wait for port to open:
          char cmd[254];
          Serial.begin(9600);
          Serial.setTimeout(5000);
          //test if the module is ready
          Serial.println("AT+RST");
          delay(1000);
          if (Serial.find("ready"))
          {
            Serial.println("Module is ready");
          }
          else
          {
            Serial.println("Module have no response.");
            while (1);
          }
          delay (1000);
          //connect to the wifi
          boolean connected = false;
          for (int i = 0; i < 5; i++)
          {
            if (connectWiFi())
            {
              connected = true;
              break;
            }
          }
          if (!connected) {
            while (1);
          }
          delay(5000);
          Serial.println("AT+CIPMUX=0");
}

void loop()
{
  //Get results from the sensor
  int DISTANCE = mySensor.read(); 
  Serial.print("Distance : ");
  Serial.print(DISTANCE);
  Serial.println(" cm");
  delay(1000);
  if(DISTANCE>10)
  {
    analogWrite(A0,HIGH);
    int num=0;
          String var = "{\"value\":"+ String(value) + "}";
          num = var.length();
          String cmd = "AT+CIPSTART=\"TCP\",\"";
          cmd += DST_IP;
          cmd += "\",80";
          Serial.println(cmd);
          if (Serial.find("Error")) return;
          
          len=strlen ("POST /api/v1.6/datasources/");
          len=len+strlen (idvariable);
          len=len+strlen ("/values HTTP/1.1\nContent-Type: application/json\nContent-Length: ");
          char numlenght[4]; // this will hold the length of num which is the length of the JSON element
          sprintf(numlenght, "%d", num); // saw this clever code off the net; works yay
          len=len+strlen (numlenght);
          len=len + num; //fixed length of the string that will print as Content-Length: in the POST
          len=len+strlen ("\nX-Auth-Token: ");
          len=len+strlen (token);
          len=len+strlen ("\nHost: things.ubidots.com\n\n");
          len=len+strlen ("\n\n");
          Serial.print("AT+CIPSEND=");
          Serial.println (len); //lenght of the entire data POST for the CIPSEND command of ESP2866
          //Serial.println(cmd.length());
          if (Serial.find(">"))
          {
            //Serial.print(">");
          } else
          {
            Serial.println("AT+CIPCLOSE");
            delay(1000);
            return;
          }
          Serial.print ("POST /api/v1.6/variables/");
          Serial.print (idvariable);
          Serial.print ("/values HTTP/1.1\nContent-Type: application/json\nContent-Length: ");
          Serial.print (num);
          Serial.print ("\nX-Auth-Token: ");
          Serial.print (token);
          Serial.print ("\nHost: things.ubidots.com\n\n");
          Serial.print (var);
          Serial.println ("\n\n");
          delay(9000);
          //Serial.find("+IPD"); clear the input buffer after the web site responds to the POST
          while (Serial.available())
          {
            char c = Serial.read();
          }
          delay(1000);
        }
        boolean connectWiFi()
        {
          Serial.println("AT+CWMODE=1");
          String cmd = "AT+CWJAP=\"";
          cmd += SSID;
          cmd += "\",\"";
          cmd += PASS;
          cmd += "\"";
          Serial.println(cmd);
          delay(2000);
          if (Serial.find("OK"))
          {
          return true;
          } else
          {
           return false;
          }
  }
}
