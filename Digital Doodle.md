
# Final project Digital Doodle: Ingy el sheikh& Fatema Nassar

This was quite a journey, with many ups and downs but definitely one that ended in relief and content! The idea of our final project revolved giving our audience a chance to go back to their childhood and just have fun! Anyone that interacts with our project is given the opportunity to just draw whatever their heart desires, and the physical interactivity (because users will draw with their hands)  gives it an extra interesting and more immersive experience. We wanted our project to give the user complete freedom, so they users can choose between 5 different brushes and use the color they want to draw using the color selector.
## Implementation:

There were mainly two aspects of this project, the processing aspect and the Arduino aspect (and serial communication). This is essentially also how we split the work between us. Fatema essentially took on the challenge of the processing side of things and I took on the challenge of the Arduino side of things and the blog documentations. So Fatema is the one that created the beautiful generative art pieces that played the role of our brushes, and she worked on the fiducial tracking as well (which was a pain! so she did a great job). While I focused on the arduino side of things, wiring up the buttons and slide potentiometer, creating the color gradient and its code on processing, and creating the serial communication between arduino and processing, I also took responsibility of the blog documentation. However, we also found ourselves working together most of the time, Fatema would help me with the arduino when I was stuck and vice versa. So it eventually turned into us working together on most of the aspects of the project rather than individually which worked out great!
## lessons learned/how to improve:

This project is one that definitely helped us grow. Naturally it was not a perfect linear growth, hence there is always room for improvement. reflecting on where we can improve is definitely an essential thing to do. After the showcase these are the things we noticed could have been executed better:

1. During our presentation to the class on the 13th, a very inconvenient and stupid mistake (we just need to move the location of the project slightly due to lighting issue) caused us to panic and not be able to show the class our project. lesson learned! we should have set up earlier especially since our project was not very straight forward and is prone to spontaneous errors.Luckily it all worked out in the end.
2. When it comes to the project itself, we found that we should have included some sort of feedback for when the buttons are pressed, just to let the person know which brush they are using.
3. We also learned that sometimes the finer details take even more time and effort than the bigger aspects of the project! and they bring the project together at the end.

## IM showcase:

Here are some of the very unique masterpieces that people created on our digital doodle in the showcase. It was so much fun seeing how different people interacted differently with our project!

<img src="https://intro.nyuadim.com/wp-content/uploads/2021/12/drawing-768x574.png" width="500"/>

And here are a few video out of many of people interacting with our project (you can see how people move in the camera on the screen)!!

Arduino code:
int left = 0;
int right = 0;
int buttonOne = 9;
int buttonTwo = 10;
int buttonThree = 11;
int buttonFour = 12;
int buttonFive = 13;
int buttonSix = 3;//reset
int buttonSeven = 5;//Draw
int buttonEight = 4;//saveScreen
int val1 = 0;
int val2 = 0;
int val3 = 0;
int val4 = 0;
int val5 = 0;
int val6 = 0;
int val7 = 0;
int val8 = 0;
void setup() {
  pinMode(buttonOne, INPUT);
  pinMode(buttonTwo, INPUT);
  pinMode(buttonThree, INPUT);
  pinMode(buttonFour, INPUT);
  //   pinMode(buttonFive, INPUT);
  Serial.begin(9600);
  Serial.println("0,0");
}
void loop() {
  while (Serial.available()) {
    right = Serial.parseInt();
    left = Serial.parseInt();
    if (Serial.read() == '\n') {
      int sensor = analogRead(A0);
      delay(1);
      val1 = digitalRead(buttonOne);
      delay(1);
      val2 = digitalRead(buttonTwo);
      delay(1);
      val3 = digitalRead(buttonThree);
      delay(1);
      //
      val4 = digitalRead(buttonFour);
      delay(1);
      val5 = digitalRead(buttonFive);
      delay(1);
      val6 = digitalRead(buttonSix);
      delay(1);
      val7 = digitalRead(buttonSeven);
      delay(1);
      
      val8 = digitalRead(buttonEight);
      delay(1);
  
      Serial.print(sensor);
      Serial.print(',');
      Serial.print(val1);
      Serial.print(',');
      Serial.print(val2);
      Serial.print(',');
      Serial.print(val3);
      Serial.print(',');
      Serial.print(val4);
      Serial.print(',');
      Serial.print(val5);
      Serial.print(',');
      Serial.print(val6);//
      Serial.print(',');
      Serial.print(val7);//
      Serial.print(',');
      Serial.println(val8);
    }
  }
}

Processing Code:
import processing.video.*;
import jp.nyatla.nyar4psg.*;
import processing.serial.*;
import java.util.*;
import processing.sound.*;
SoundFile beep;
int n=1, mode=1;
PVector mouseloc = new PVector(0, 0);
PVector p;
PImage img;
PGraphics pg;
Capture cam;
MultiMarker nya;
PImage save;
Brush myBrush = new Brush(1, 2, mouseloc);
// serial stuff
Serial myPort;
int xPos=0;
int yPos;
boolean onOff=false;
boolean onOff2=false;
PImage myImage;
color pix;
int [] val = {0, 0, 0, 0, 0, 0, 0, 0};
int [] valprev = {0, 0, 0, 0, 0, 0, 0, 0};
void setup() {
  //size(1280, 720, P3D);
  colorMode(RGB, 100);
  fullScreen(P3D);
  printArray(Serial.list());
  String portname=Serial.list()[2];
  println(portname);
  myPort = new Serial(this, portname, 9600);
  myPort.clear();
  myPort.bufferUntil('\n');
  myImage  = loadImage("color.jpeg");
  //img = createImage(1920, 1080);
  //printArray(Capture.list());
  //cam=new Capture(this, 1920, 1080);
  pg = createGraphics(1440, 900, P3D);
  cam = new Capture(this, 640, 360);
  // String[] devices = Capture.list();
  //println(devices);
  print(cam.width +"  "+cam.height);
  cam.start();
  pg.beginDraw();
  pg.background(0);
  pg.loadPixels();
  pg.endDraw();
  nya=new MultiMarker(this, width, height, "data/camera_para.dat", NyAR4PsgConfig.CONFIG_PSG);
  nya.addARMarker("data/patt.hiro", 40);//id=0
  cam.start();
  background(0);
  loadPixels();
  //beep= new SoundFile(this,"Buzzer-sound.mp3");
  beep= new SoundFile(this,"beep-09.wav");
   
}
int c=0;
void draw() {
  c++;
  if (cam.available() !=true) {
    return;
  }
  background(255);
  if (cam.available() == true) {
    cam.read();
  }
  pg.beginDraw();
  //pg.background(0);
  pg.loadPixels();
  pg.translate(width, 0);
  pg.scale(-1, 1);
  pg.image(cam, 0, 0, width, height);
  pg.endDraw();
  nya.detect(pg);
  imageMode(CORNER);
  //nya.drawBackground(pg);
  updatePixels();
  image(pg,0,height-height/3,width/4,height/4);
  
  if ((!nya.isExist(0))) {
    return;
  }
  p=nya.screen2ObjectCoordSystem(0, width/2, height/2);
  image(pg,0,height-height/3,width/4,height/4);
  updatePixels();
  if (mode==1) {
    drawScreen();
  }
  // nya.endTransform();
  else if (mode==2) {
    loadPixels();
    save("pic"+String.valueOf(n++)+".jpeg");
    delay(10);
    loadPixels();
    takeScreenShot();
    mode=1;
    delay(500);
    mode=3;
  }
  else if(mode==3){
    //display
    pg.beginDraw();
    pg.background(200);
    pg.imageMode(CENTER);
    for(int i=1; i<n+1; i++){
      save = loadImage("pic"+i+".jpeg");
      pushMatrix();
      rotate(random(0,HALF_PI));
      float widthh=random(width/6,width/4), heightt=random(height/6,height/4),locX=random(width),locY=random(height);
      pg.image(save,locX, locY);
      popMatrix();
    }
  }
}
void drawScreen() {
  //brush color picker
  imageMode(CENTER);
  fill(0);
  rectMode(CENTER);
  noStroke();
  rect(myImage.width/2, myImage.height/2, myImage.width, myImage.height*1.5);
  image(myImage, myImage.width/2, myImage.height/2, myImage.width, myImage.height);
  color pix = get(xPos, myImage.height/2);
  fill(pix);
  stroke(0);
  rect(xPos, myImage.height/2, 10, myImage.height*1.5);
  buttonPressed();
  myBrush.setCol(pix);
  mouseloc.x = p.x;
  mouseloc.y = p.x;
  myBrush.draww(mouseloc);
  loadPixels();
}
//void sound(){
//  if (valprev[0] == 1) {
//   beep.play();
//  }
////  } else if (valprev[1] == 1 ) {
////    buzzer.play();
////  } else if (valprev[2]==1 ) {
////    beep.play();
////  } else if (valprev[3]==1 ) {
////     beep.play();
////  } else if (valprev[4]==1 ) {
////   beep.play();
////  }
////  // reset
////  else if (valprev[5]==1 ) {
////beep.play();
////    }
////  else if (valprev[6]==1 ) { //draw
////beep.play();
////  } else if (valprev[7]==1 ) { //save screen
////   beep.play();
////  }
////}
    
  
//  }
void buttonPressed() {
  if (val[0] == 1 ) {
    myBrush.setbrush(1);
   
  } else if (val[1] == 1 ) {
    myBrush.setbrush(2);
  } else if (val[2]==1 ) {
    myBrush.setbrush(3);
  } else if (val[3]==1 ) {
    myBrush.setbrush(4);
  } else if (val[4]==1 ) {
    myBrush.setbrush(5);
  }
  // reset
  else if (val[5]==1 ) {
    println("bla");
    background(0);
    loadPixels();
    //for (int i=0; i<width*height; i++) {
    //  pixels[i]=color(0);
    //}
  } else if (val[6]==1 ) { //draw
    mode=1;
  } else if (val[7]==1 ) { //save screen
    mode=2;
  }
}
void takeScreenShot(){
  rectMode(CENTER);
  stroke(50);
  rect(width/2, height/2, width,height);
  delay(100);
  
}
void serialEvent(Serial myPort) {
  String s=myPort.readStringUntil('\n');
  s=trim(s);
  if (s!=null) {
    println(s);
    int values[]=int(split(s, ','));
    if (values.length==9) {
      xPos=(int)map(values[0], 89, 912, 0, myImage.width);
      //yPos=(int)map(values[1],0,1023,0, height);
      //yPos=height/2-230;
      //myBrush.setbrush(2);
      val[0] = (int)values[1];  // read it and store it in val[]
      val[1] = (int)values[2];
      val[2] = (int)values[3];
      val[3] = (int)values[4];
      val[4] = (int)values[5];
      val[5] = (int)values[6];//reset
      val[6] = (int)values[7];//draw
      val[7] = (int)values[8];//saveScreen
    }
    myPort.write(int(onOff)+","+int(onOff2)+"\n");
  }
  for(int i=0;i<8;i++){
    valprev[i]=val[i];
  }
}
class Brush {
  int brushN;
  float stepSize, linelength, diam;
  float angle;
  float x, y, tempX, tempY;
  color col;
  //
  int counter;
  PFont font;
  float fontSize;
  String letters;
  float  fontSizeMin;
  //
  //linemoduelw
  PShape lineModule;
  float [] numX={ -100, -70, 0, 70, 100, 70, 0, -70};
  float [] numY={ 0, 70, 100, 70, 0, -70, -100, -70};
  Brush(int defaultt, float step_size, PVector mouse) {
    // 1 blur
    // 2 random cylinder
    // 3 dynamic shapes
    // 4 text lines
    // 5 rotating lines
    brushN = defaultt;
    stepSize = step_size;
    angle = 270;
    x = mouseX;
    y = mouse.y;
    col = color(255);
    linelength = 100;
    diam = 2;
    fontSizeMin=200;
    fontSize=50;
    letters = "It isn't true that my mattress is made of cotton candy. It was the first time he had ever seen someone cook dinner on an elephant. Homesickness became contagious in the young campers' cabin. Every manager should be able to recite at least ten nursery rhymes backward.";
  }
  void setbrush(int brushn) {
    brushN=brushn;
  }
  void setSize(float size) {
    stepSize=size;
  }
  void setCol(int colorr) {
    col=colorr;
  }
  void setLineLength(float len) {
    linelength=len;
  }
  void setDiam(float diamm) {
    diam= diamm;
  }
  void setAngle(float ang) {
    angle=ang;
  }
  void draww(PVector mouse) {
    switch (brushN) {
    case 2:
      setLineLength(100);
      setSize(5);
      setDiam(2);
      blur(mouse);
      break;
    case 3:
      setLineLength(100);
      setSize(3);
      randomprisms(mouse);
      break;
    case 4:
      dynamicShapes(mouse);
      break;
    case 5:
      textlines(mouse);
      break;
    default :
      setLineLength(100);
      strokeWeight(1);
      rotatingLines(mouse);
      break;
    }
  }
  void helper_randomPrisms() {
    float angle = TWO_PI/8;
    float rad = 100 * random(0.5, 1);
    for (int i=0; i<8; i++) {
      numX[i]=cos(angle * i) * rad;
      numY[i]=sin(angle * i) * rad;
    }
  }
  void blur(PVector mouse) {
    float [] blurX ={ -stepSize, -stepSize, 0, stepSize, stepSize, stepSize, 0, -stepSize};
    float [] blurY ={         0, stepSize, stepSize, stepSize, 0, -stepSize, -stepSize, -stepSize};
    float d = dist(x, y, mouse.x, mouse.y);
    if (d > stepSize) {
      // gets the angle between the mouse and the location of the brush so that next point is drawn in the right intended location
      angle = (float)Math.atan2(mouse.y-y, mouse.x-x);
      tempX = x;
      tempY = y;
      nya.beginTransform(0);
      //
      //translate(x, y);
      rotate(angle);
      noStroke();
      fill(col, 40);
      //
      for (int i=0; i<500; i++) {
        int dir =  int(random(0, 7));
        rotate(random(5));
        println(blurX[dir]);
        tempX=blurX[dir]*random(1, 10);
        tempY=blurY[dir]*random(1, 10);
        circle(tempX, tempY, diam);
      }
      //
      nya.endTransform();
      x = mouse.x;
      y = mouse.y;
    }
  }
  void randomprisms(PVector mouse) {
    float d = dist(x, y, mouse.x, mouse.y);
    if (d>stepSize) {
      // gets the angle between the mouse and the location of the brush so that next point is drawn in the right intended location
      angle = (float)Math.atan2(mouse.y-y, mouse.x-x);
      for ( int i=0; i<8; i++) {
        numX[i]+=random(0-stepSize/2, stepSize/2);
        numY[i]+=random(0-stepSize/2, stepSize/2);
      }
      nya.beginTransform(0);
      //
      //translate(x, y);
      rotate(angle+random(-0.1, 0.1));
      stroke(col);
      strokeWeight(stepSize/4);
      rectMode(CENTER);
      noFill();
      rect(0, 0, linelength*random(0.95, 1)*d/100, linelength*random(0.95, 1)*d/100);
      nya.endTransform();
      x=x+(float)Math.cos((angle))*stepSize;
      y=y+(float)Math.sin((angle))*stepSize;
    }
    helper_randomPrisms();
  }
  void dynamicShapes(PVector mouse) {
    float d = dist(x, y, mouse.x, mouse.y);
    println("x: ", x, "y", y, " mouse.x: ", mouse.x, "mouse.y: ", mouse.y);
    if (d>stepSize) {
      //
      lineModule = createShape();
      stroke(col);
      strokeWeight(1);
      noFill();
      lineModule.beginShape();
      lineModule.vertex(100, 0); // first point
      lineModule.vertex(0, 100);// /
      lineModule.vertex(-100, 0);// \
      lineModule.vertex(0, -100);// /
      lineModule.vertex(100, 0);// \
      lineModule.endShape();
      // gets the angle between the mouse and the location of the brush so that next point is drawn in the right intended location
      angle = (float)Math.atan2(mouse.y-y, mouse.x-x);
      nya.beginTransform(0);
      //
      //translate(mouse.x, mouse.y);
      rotate((angle-PI));
      shape(lineModule, 0, 0, d, noise(1+frameCount*0.0001)*10);
      //
      nya.endTransform();
      x=x+(float)Math.cos((angle))*stepSize;
      y=y+(float)Math.sin((angle))*stepSize;
    }
  }
  void textlines(PVector mouse) {
    float d = dist(x, y, mouse.x, mouse.y);
    if (d>stepSize) {
      // gets the angle between the mouse and the location of the brush so that next point is drawn in the right intended location
      angle = (float)Math.atan2(mouse.y-y, mouse.x-x);
      for ( int i=0; i<8; i++) {
        numX[i]+=random(-4, 4);
        numY[i]+=random(-4, 4);
      }
      nya.beginTransform(0);
      //
      //translate(x, y);
      rotate(angle+random(-0.1, 0.1));
      stroke(col);
      strokeWeight(stepSize/4);
      noFill();
      beginShape();
      curveVertex(numX[0], numY[0]);///////////////
      curveVertex(numX[0], numY[0]);///////////////
      curveVertex(numX[1], numY[1]);//mid
      curveVertex(numX[2], numY[2]);///////////////
      curveVertex(numX[3], numY[3]);// mid
      curveVertex(numX[4], numY[4]);///////////////
      curveVertex(numX[5], numY[5]);//mid
      curveVertex(numX[6], numY[6]);///////////////
      curveVertex(numX[7], numY[7]);//mid
      curveVertex(numX[0], numY[0]);///////////////
      curveVertex(numX[0], numY[0]);///////////////
      endShape();
      nya.endTransform();
      x=x+(float)Math.cos((angle))*stepSize;
      y=y+(float)Math.sin((angle))*stepSize;
    }
  }
  void rotatingLines(PVector mouse) {
    nya.beginTransform(0);
    strokeWeight(1);
    stroke(col);
    rotate(radians(angle));
    line(0, 0, linelength, 0);
    //
    nya.endTransform();
    angle +=5;
    linelength= int(noise(50+0.01*frameCount)*300);
  }
}
