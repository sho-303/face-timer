# face-timer
Face training interval timer
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>表情筋トレーニングタイマー</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:Arial,sans-serif;
}

body{
    background:#1976d2;
    color:white;
    display:flex;
    justify-content:center;
    align-items:center;
    height:100vh;
    transition:0.3s;
}

.container{
    width:90%;
    max-width:500px;
    text-align:center;
}

h1{
    margin-bottom:20px;
}

.circle{
    width:260px;
    height:260px;
    border:12px solid white;
    border-radius:50%;
    display:flex;
    justify-content:center;
    align-items:center;
    margin:20px auto;
    font-size:72px;
    font-weight:bold;
}

#phase{
    font-size:28px;
    margin-bottom:15px;
}

#exercise{
    font-size:24px;
    margin-top:15px;
    min-height:60px;
}

#next{
    margin-top:10px;
    font-size:18px;
    opacity:0.8;
}

#progress{
    margin-top:20px;
    font-size:20px;
}

button{
    margin-top:30px;
    padding:15px 40px;
    font-size:22px;
    border:none;
    border-radius:12px;
    cursor:pointer;
}
</style>
</head>

<body>

<div class="container">

<h1>表情筋トレーニング</h1>

<div id="phase">開始を押してください</div>

<div class="circle">
<div id="timer">3</div>
</div>

<div id="exercise"></div>

<div id="next"></div>

<div id="progress"></div>

<button onclick="startWorkout()" id="startBtn">
開始
</button>

</div>

<script>

const exercises=[
"あーいートレーニング",
"おーうートレーニング",
"ベロで口の周りを回すローリング運動",
"受け口（しゃくれ）にして首の筋を伸ばす運動",
"口をすぼめたまま斜め後ろに引っ張る運動",
"反対側の斜め後ろに引っ張る運動",
"耳の下から首筋にかけての揉みほぐし",
"指先で頬を持ち上げて回す運動",
"肩甲骨を寄せながら頭を後ろに倒す運動"
];

let index=0;
let time=3;
let mode="countdown";
let interval;
function beep(){

    try{

        const ctx=new(window.AudioContext||window.webkitAudioContext)();

        const osc=ctx.createOscillator();

        const gain=ctx.createGain();

        osc.connect(gain);

        gain.connect(ctx.destination);

        osc.frequency.value=800;

        osc.start();

        gain.gain.exponentialRampToValueAtTime(0.0001,ctx.currentTime+0.15);

        osc.stop(ctx.currentTime+0.15);

    }catch(e){}

}

function updateScreen(){

    document.getElementById("timer").innerText=time;

    document.getElementById("progress").innerText=
    (index+1)+" / "+exercises.length;

    if(mode==="work"){

        document.body.style.background=time<=5?"#d32f2f":"#1976d2";

        document.getElementById("phase").innerText="運動";

        document.getElementById("exercise").innerText=exercises[index];

        document.getElementById("next").innerText=
        index<exercises.length-1?
        "次："+exercises[index+1]:"最後の種目です";

    }

    else if(mode==="rest"){

        document.body.style.background="#fb8c00";

        document.getElementById("phase").innerText="休憩";

        document.getElementById("exercise").innerText="";

        document.getElementById("next").innerText=
        "次："+exercises[index+1];

    }

    else{

        document.body.style.background="#455a64";

        document.getElementById("phase").innerText="開始まで";

    }

}

function startWorkout(){

    document.getElementById("startBtn").style.display="none";

    updateScreen();

    interval=setInterval(runTimer,1000);

}

function runTimer(){

    time--;

    if(time>=1 && time<=3){

        beep();

    }

    if(time<0){

        if(mode==="countdown"){

            mode="work";

            time=60;

        }

        else if(mode==="work"){

            if(index===exercises.length-1){

                clearInterval(interval);

                document.body.style.background="#2e7d32";

                document.getElementById("phase").innerText="🎉 トレーニング終了！";

                document.getElementById("timer").innerText="0";

                document.getElementById("exercise").innerText="お疲れさまでした！";

                document.getElementById("next").innerText="";

                document.getElementById("progress").innerText="";

                beep();

                return;

            }

            mode="rest";

            time=10;

        }

        else if(mode==="rest"){

            index++;

            mode="work";

            time=60;

        }

        beep();

    }

    updateScreen();

}

updateScreen();

</script>

</body>
</html>
