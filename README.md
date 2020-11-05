# MusicComposer_MiniProject
Cp2-fa20 Mini Project

// Copyright (c) 2019 ml5
//
// This software is released under the MIT License.
// https://opensource.org/licenses/MIT

/* ===
ml5 Example
PoseNet example using p5.js
=== */


// Bouncing Bells
// preload sounds at several pitches


var sounds = [];
let setVolume = 0.5;

function preload() {
  soundFormats('m4a');
  for (var i = 0; i < 20; i++) {
    let sound = loadSound('assets/_Music box, D# 1 (ID 1869)_BSB.m4a');
    sound.rate(1 * pow(2, i / 12)); // 12-semitone exponential scale
    sounds.push(sound);
    sound.setVolume(0.2);
    {
    

    }

  }
}


let video;
let poseNet;
let poses = [];

var px;
var py;
var vpx;
var vpy;
var puck;
var x_d;
var y_d;



function setup() {
  createCanvas(400, 400);
  video = createCapture(VIDEO);
  video.size(width, height);

  // Create a new poseNet method with a single detection
  poseNet = ml5.poseNet(video, modelReady);
  // This sets up an event that fills the global variable "poses"
  // with an array every time new poses are detected
  poseNet.on('pose', function(results) {
    poses = results;
  });
  // Hide the video element, and just show the canvas
  video.hide();
  setupBells();
  setupPuck();
}

function modelReady() {
  select('#status').html('Model Loaded');
}

function draw() {
  image(video, 0, 0, width, height);


  // We can call both functions to draw all keypoints and the skeletons
  // drawKeypoints();
  drawBells();
  drawPuck();
}

//  for (let i = 0; i < poses.length; i++) {
//   print(poses[0].pose.nose.y);
//  print(poses[0].pose.leftWrist.x);
// }
//print(poses);
//}

// A function to draw ellipses over the detected keypoints
function drawKeypoints() {
  // Loop through all the poses detected
  for (let i = 0; i < poses.length; i++) {
    // For each pose detected, loop through all the keypoints
    let pose = poses[i].pose;
    for (let j = 0; j < pose.keypoints.length; j++) {
      // A keypoint is an object describing a body part (like rightArm or leftShoulder)
      let keypoint = pose.keypoints[j];
      // Only draw an ellipse is the pose probability is bigger than 0.2
      if (keypoint.score > 0.2) {
        //fill(255, 0, 0);
        noStroke();
        //ellipse(keypoint.position.x, keypoint.position.y, 10, 10);


      }
    }
  }
}



// make some initial bells ------------------
var circles = [];

function setupBells() {
  createCanvas(400, 400);
  for (let i = 0; i < 12; i++) {
    // new "circle" object, with x, y, vx, vy, and size properties:
    circles.push({
      x: random(50, width - 50),
      y: random(50, height - 50),
      vx: random(-0.3, 0.3),
      vy: random(-0.3, 0.3),
      size: 10 + round(random(15))
    });
  }
}

function drawBells() {
  background(0);
  noStroke();


  for (let i = 0; i < circles.length; i++) {
    // get circle object
    let circle = circles[i];

    // draw it
    ellipse(circle.x, circle.y, circle.size);

    // move it according to speed
    circle.x += circle.vx;
    circle.y += circle.vy;

    // check boundaries and update directions
    if (circle.x > width - circle.size / 2 || circle.x < circle.size / 2) {
      circle.vx = -circle.vx;
      sounds[circle.size - 10].play();
    }
    if (circle.y > height - circle.size / 2 || circle.y < circle.size / 2) {
      circle.vy = -circle.vy;
      sounds[circle.size - 10].play();
    }

    // check collisions with other circles
    for (let j = i + 1; j < circles.length; j++) {
      if (dist(circle.x, circle.y, circles[j].x, circles[j].y) < circle.size / 2 + circles[j].size / 2) {
        circle.vx = -circle.vx;
        circle.vy = -circle.vy;
      }
    }
  }
}


function setupPuck() {

  px = width / 2;
  py = height / 2;
  //let poses = [];
  vpx = random(3, 2);
  vpy = random(2, 3);
  puck = 30;
  x_d = 0;
  y_d = 0;

}


function drawPuck() {

  for (let i = 0; i < circles.length; i++) {
    // get circle object
    let circle = circles[i]


    if (dist(px, py, circle.x, circle.y) < 30) {
      fill(255);
      x_d = circle.x - px;
      y_d = circle.y - py;
      circle.vx += 1 / (Math.sign(x_d) * (abs(x_d) + 1));
      circle.vy += 1 / (Math.sign(x_d) * (abs(x_d) + 1));
    }
  }


  if (poses.length > 0) {
    px = poses[0].pose.nose.x;
    py = poses[0].pose.nose.y;
  }
   

  
  fill(255);
  ellipse(px, py, puck);
  //px += vpx;
  //py += vpy;




  //bonce from walls
  if (py < 15) {
    vpy = -vpy;
  }
  if (py > height - 15) {
    vpy = -vpy;
  }

  if (px < 15) {
    vpx = -vpx;
  }
  if (px > width - 15) {
    vpx = -vpx;
  }

  fill(255, 100, 100);

}
