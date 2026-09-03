let video;
let poseNet;
let poses = [];

let footprints = [];

let people = [];
let nextId = 0;

// 足跡を出す間隔
let footprintInterval = 90;

// 足跡の大きさ
let footprintSize = 150;

// =====================================
// 足跡画像
// =====================================

let footprintImages = [];


// =====================================
// 画像を読み込む
// =====================================

function preload() {

  footprintImages.push(loadImage("足跡黄.png"));
  footprintImages.push(loadImage("足跡赤.png"));
  footprintImages.push(loadImage("足跡青.png"));
  footprintImages.push(loadImage("足跡緑.png"));
  footprintImages.push(loadImage("足跡紫.png"));
  footprintImages.push(loadImage("足跡橙.png"));

}


// =====================================
// セットアップ
// =====================================

function setup() {

  createCanvas(windowWidth, windowHeight);

  video = createCapture(VIDEO);
  video.size(windowWidth, windowHeight);
  video.hide();

  poseNet = ml5.poseNet(video, () => {
    console.log("Ready!");
  });

  poseNet.on("pose", (results) => {
    poses = results;
  });

}


// =====================================
// メイン
// =====================================

function draw() {

  background(0);

  // カメラ映像
  drawingContext.shadowBlur = 0;
  image(video, 0, 0, width, height);

  let updatedPeople = [];

  // PoseNetの座標を画面サイズに合わせる
  let sx = width / video.width;
  let sy = height / video.height;


  // =====================================
  // 人を検出
  // =====================================

  for (let i = 0; i < poses.length; i++) {

    let pose = poses[i].pose;

    if (
      pose.leftAnkle &&
      pose.rightAnkle &&
      pose.score > 0.2
    ) {

      // 左足
      let lx = pose.leftAnkle.x * sx;
      let ly = pose.leftAnkle.y * sy;

      // 右足
      let rx = pose.rightAnkle.x * sx;
      let ry = pose.rightAnkle.y * sy;


      // 人の中心
      let mx = (lx + rx) / 2;
      let my = (ly + ry) / 2;


      // 同じ人を探す
      let person = findPerson(mx, my);


      // =====================================
      // 新しい人
      // =====================================

      if (!person) {

        person = {

          id: nextId++,

          leftX: lx,
          leftY: ly,

          rightX: rx,
          rightY: ry,

          // 最後に足跡を出したフレーム
          lastFootprintFrame: frameCount

        };

      }


      // =====================================
      // すでにいる人
      // =====================================

      else {

        // 左足の移動距離
        let leftMove = dist(
          lx,
          ly,
          person.leftX,
          person.leftY
        );


        // 右足の移動距離
        let rightMove = dist(
          rx,
          ry,
          person.rightX,
          person.rightY
        );


        // =====================================
        // 一定フレームごとに足跡
        // =====================================

        if (
          frameCount - person.lastFootprintFrame >= footprintInterval &&
          (leftMove > 25 || rightMove > 25)
        ) {

          // より大きく動いた足に足跡を出す

          if (leftMove > rightMove) {

            addFootprint(
              lx,
              ly,
              "left"
            );

          } else {

            addFootprint(
              rx,
              ry,
              "right"
            );

          }


          // 足跡を出したフレームを記録
          person.lastFootprintFrame = frameCount;
        }


        // 足の位置を更新
        person.leftX = lx;
        person.leftY = ly;

        person.rightX = rx;
        person.rightY = ry;
      }


      updatedPeople.push(person);
    }
  }


  people = updatedPeople;


  // =====================================
  // 足跡を描画
  // =====================================

  for (let f of footprints) {

    drawFootprint(f);

  }


  // 影を完全にOFF
  drawingContext.shadowBlur = 0;

}


// =====================================
// 足跡を追加
// =====================================

function addFootprint(x, y, side) {

  // =====================================
  // 6種類の足跡からランダムに選ぶ
  // =====================================

  let randomImage = random(footprintImages);


  footprints.push({

    x: x,

    y: y,

    side: side,

    image: randomImage

  });

}


// =====================================
// 足跡を描画
// =====================================

function drawFootprint(f) {

  push();

  translate(f.x, f.y);

  imageMode(CENTER);


  // =====================================
  // 元画像の縦横比を計算
  // =====================================

  let ratio =
    f.image.height /
    f.image.width;


  let w = footprintSize;

  let h =
    footprintSize * ratio;


  // =====================================
  // 左足
  // =====================================

  if (f.side === "left") {

    image(
      f.image,
      0,
      0,
      w,
      h
    );

  }


  // =====================================
  // 右足
  // 左足画像を左右反転
  // =====================================

  else {

    scale(-1, 1);

    image(
      f.image,
      0,
      0,
      w,
      h
    );

  }


  pop();

}


// =====================================
// 人物追跡
// =====================================

function findPerson(x, y) {

  let threshold = 100;

  let closest = null;

  let minDist = threshold;


  for (let p of people) {

    let personX =
      (p.leftX + p.rightX) / 2;

    let personY =
      (p.leftY + p.rightY) / 2;


    let d = dist(
      x,
      y,
      personX,
      personY
    );


    if (d < minDist) {

      minDist = d;

      closest = p;

    }

  }


  return closest;

}


// =====================================
// ウィンドウサイズ変更
// =====================================

function windowResized() {

  resizeCanvas(
    windowWidth,
    windowHeight
  );

  video.size(
    windowWidth,
    windowHeight
  );

}


// =====================================
// Fキーで全画面
// =====================================

function keyPressed() {

  if (key === "f" || key === "F") {

    fullscreen(
      !fullscreen()
    );

  }

}


// =====================================
// マウスクリックで全画面
// =====================================

function mousePressed() {

  fullscreen(true);

}
