<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Latein Vokabeltrainer</title>

<style>
    body{
        font-family: Arial, sans-serif;
        background:#f4f4f4;
        text-align:center;
        padding:20px;
    }

    .container{
        background:white;
        max-width:600px;
        margin:auto;
        padding:30px;
        border-radius:15px;
        box-shadow:0 0 10px rgba(0,0,0,0.2);
    }

    h1{
        margin-bottom:20px;
    }

    #frage{
        font-size:24px;
        margin-bottom:20px;
    }

    input{
        width:90%;
        padding:12px;
        font-size:18px;
        border-radius:10px;
        border:1px solid gray;
    }

    button{
        margin-top:15px;
        padding:12px 25px;
        font-size:18px;
        border:none;
        border-radius:10px;
        cursor:pointer;
        background:#007bff;
        color:white;
    }

    button:hover{
        background:#0056b3;
    }

    #feedback{
        margin-top:20px;
        font-size:20px;
        font-weight:bold;
    }

    #fortschritt{
        margin-top:15px;
    }

    #score{
        margin-top:15px;
    }
</style>
</head>
<body>

<div class="container">

    <h1>Latein Vokabeltrainer</h1>

    <div id="frage"></div>

    <input type="text" id="eingabe" placeholder="Antwort eingeben">

    <br>

    <button onclick="pruefen()">Antwort prüfen</button>

    <div id="feedback"></div>

    <div id="fortschritt">Fortschritt: 0%</div>

    <div id="score">Punkte: 0</div>

    <div id="bestscore"></div>

</div>

<script>

const vokabeln = [
    ["bellum gerere / bellum inferre", "Krieg führen, Krieg beginnen"],
    ["pacem / amicitiam confirmare", "Frieden, Freundschaft schließen"],
    ["iter facere", "marschieren"],
    ["arma capere", "zu den Waffen greifen"],
    ["consilium capere", "einen Entschluss fassen"],
    ["libertatis causa", "um der Freiheit willen"],
    ["proelio interesse", "an der Schlacht teilnehmen"],
    ["proelium committere", "eine Schlacht liefern"]
];

vokabeln.sort(() => Math.random() - 0.5);

let index = 0;
let punkte = 0;

function normalize(text){
    text = text.toLowerCase();
    text = text.replace(/\(.*?\)/g, "");
    text = text.replace(/perf\./g, "");
    text = text.replace(/;/g, ",");

    let parts = text.split(",")
        .map(p => p.trim())
        .filter(p => p !== "");

    return new Set(parts);
}

function checkAnswer(userInput, correct){

    let userSet = normalize(userInput);
    let correctSet = normalize(correct);

    let richtige = [...userSet].filter(x => correctSet.has(x));

    return {
        teilpunkte: richtige.length,
        maxPunkte: correctSet.size,
        loesung: [...correctSet]
    };
}

function naechsteFrage(){

    if(index >= vokabeln.length){

        let prozent = Math.round((punkte / vokabeln.length) * 100);

        document.getElementById("frage").innerHTML =
            `Fertig! ${punkte}/${vokabeln.length} (${prozent}%)`;

        document.getElementById("eingabe").style.display = "none";

        return;
    }

    document.getElementById("frage").innerText =
        vokabeln[index][0];

    document.getElementById("eingabe").value = "";
}

function pruefen(){

    let userInput =
        document.getElementById("eingabe").value;

    let correct = vokabeln[index][1];

    let result = checkAnswer(userInput, correct);

    punkte += result.teilpunkte;

    let feedback = document.getElementById("feedback");

    if(result.teilpunkte === result.maxPunkte){

        feedback.innerHTML =
            `Perfekt! +${result.teilpunkte} Punkte ✅`;

        feedback.style.color = "green";

    } else if(result.teilpunkte > 0){

        feedback.innerHTML =
            `Teilweise richtig! +${result.teilpunkte}/${result.maxPunkte} 🟡`;

        feedback.style.color = "orange";

    } else {

        feedback.innerHTML =
            `Falsch ❌ → ${result.loesung.join(", ")}`;

        feedback.style.color = "red";
    }

    index++;

    let fortschritt =
        Math.round((index / vokabeln.length) * 100);

    document.getElementById("fortschritt").innerText =
        `Fortschritt: ${fortschritt}%`;

    document.getElementById("score").innerText =
        `Punkte: ${punkte}`;

    setTimeout(naechsteFrage, 800);
}

naechsteFrage();

</script>

</body>
</html>
