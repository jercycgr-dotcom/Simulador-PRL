[index.html](https://github.com/user-attachments/files/22006693/index.html)
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Detecta el Riesgo - Departamento PRL Mixer & Pack</title>
<style>
  body { font-family: Arial, sans-serif; background: linear-gradient(to bottom, #1E90FF, #87CEFA); margin:0; padding:0; display:flex; justify-content:center; }
  .container { max-width: 800px; margin:30px auto; background: rgba(255,255,255,0.95); padding:25px; border-radius:15px; box-shadow:0 6px 20px rgba(0,0,0,0.3); text-align:center; }
  h1 { color:#333; }
  .question { font-size:20px; margin-top:20px; }
  .emoji { font-size:80px; display:block; margin:15px auto; animation: float 3s ease-in-out infinite; }
  @keyframes float { 0%,100% {transform: translateY(0px);} 50% {transform: translateY(-10px);} }
  .options { display:flex; flex-direction:column; margin-top:20px; }
  button { padding:14px; margin:8px 0; border:none; border-radius:8px; font-size:16px; cursor:pointer; transition: all 0.3s ease; }
  button:hover { transform: scale(1.05); }
  .correct { background-color:#4CAF50; color:#fff; }
  .partial { background-color:#FF9800; color:#fff; }
  .incorrect { background-color:#f44336; color:#fff; }
  .feedback { margin-top:20px; font-weight:bold; font-size:18px; opacity:0; transition: opacity 0.5s, transform 0.5s; }
  .feedback.show { opacity:1; transform: scale(1.1); }
  .score { text-align:center; font-size:24px; color:#333; margin-top:20px; }
  .ranking { margin-top:15px; font-size:18px; color:#333; }
  .progress-bar { height:20px; background:#ccc; border-radius:10px; overflow:hidden; margin-top:15px; }
  .progress { height:100%; width:0%; background:#4CAF50; transition: width 0.5s; }
</style>
</head>
<body>
<div class="container">
  <h1>Simulador de Seguridad: Detecta el Riesgo</h1>
  <div class="progress-bar"><div class="progress" id="progress"></div></div>
  <div id="quiz"></div>
</div>

<script>
const questions = [
  {q:"Escenario 1: Estás usando las escaleras de acceso a Mixer & Pack.", emoji:"🏬🪜", options:[
    {text:"Bajar/subir rápido sin usar pasamanos", type:"incorrect", feedback:"Bajar sin sujetarte es peligroso."}, 
    {text:"Sujetarte del pasamanos", type:"correct", feedback:"¡Bien! Sujetarte previene caídas."},
    {text:"Saltar el escalón", type:"partial", feedback:"Saltar aumenta el riesgo de caída."}
  ]},
  {q:"Escenario 2: Mover un palet pesado.", emoji:"📦", options:[
    {text:"Pedir ayuda", type:"correct", feedback:"¡Bien! Usar ayuda protege tu espalda."},
    {text:"Levantar el palet solo", type:"incorrect", feedback:"Nunca levantes cargas pesadas solo."}, 
    {text:"Inclinarse para levantar el palet solo", type:"partial", feedback:"Podrías lastimarte la espalda."} 
  ]},
  {q:"Escenario 3: Ajustar máquina en funcionamiento.", emoji:"⚙️", options:[
    {text:"Seguir procedimiento", type:"correct", feedback:"¡Bien! Seguir procedimientos evita accidentes."},
    {text:"Ignorar procedimiento", type:"incorrect", feedback:"Ignorar procedimientos es muy peligroso."},
    {text:"Si ya sabes como hacerlo no hay que seguir procedimiento", type:"partial", feedback:"Puedes sufrir un atrapamiento."} 
  ]},
  {q:"Escenario 4: Quitar seguridad de máquina.", emoji:"🔒", options:[
    {text:"Nunca quitar seguridades", type:"correct", feedback:"¡Bien! Nunca se deben quitar."},
    {text:"Quitar si parece rápido", type:"partial", feedback:"Aun así aumenta riesgo."},
    {text:"Quitar y trabajar con cuidado", type:"incorrect", feedback:"Muy peligroso."}
  ]},
  {q:"Escenario 5: Manipular agua caliente.", emoji:"💧🔥", options:[
    {text:"Usar EPIs adecuados", type:"correct", feedback:"¡Bien! Previene quemaduras."}, 
    {text:"Ir rápido sin guantes, pantalla facial, mandil de seguridad", type:"incorrect", feedback:"Riesgo de quemadura."}, 
    {text:"Cubrirse parcialmente", type:"partial", feedback:"No garantiza protección."}
  ]},
  {q:"Escenario 6: Caminando en zona de riesgo, poco espacio, mucho material.", emoji:"🪑📏", options:[ 
    {text:"Mantener distancia", type:"correct", feedback:"¡Bien! Evita golpes."},
    {text:"Ir rápido sin mirar", type:"incorrect", feedback:"Provoca accidentes."},
    {text:"Mover muebles sin comunicar", type:"partial", feedback:"Riesgo de accidente."} 
  ]},
  {q:"Escenario 7: Sobrepasar área de trabajo, cintas transportadoras.", emoji:"📦↗️", options:[ 
    {text:"Nunca sobrepasar las cintas transportadoras", type:"correct", feedback:"¡Bien! Evita lesiones."}, 
    {text:"Estirarse peligrosamente", type:"incorrect", feedback:"Riesgo de tirones y atrapamiento"}, 
    {text:"Intentar hacerlo rápido", type:"partial", feedback:"Puede causar atrapamientos."} 
  ]},
  {q:"Escenario 8: Palets y cajas en el pasillo.", emoji:"📦", options:[ 
    {text:"Mantener despejado el área de tránsito", type:"correct", feedback:"¡Bien! Previene caídas."}, 
    {text:"Saltar entre palets", type:"incorrect", feedback:"Puede causar accidentes."},
    {text:"Ignorar palets", type:"partial", feedback:"Aumenta riesgo de caída."}
  ]},
  {q:"Escenario 9: Tirón en la espalda al paletizar.", emoji:"💪📦", options:[
    {text:"Usar técnica correcta de levantamiento", type:"correct", feedback:"¡Bien! Evita lesiones lumbares."}, 
    {text:"Continuar el trabajo sin descanso", type:"incorrect", feedback:"Provoca dolor."}, 
    {text:"No usar ayudas mecánicas, demora demasiado", type:"partial", feedback:"Posibles lesiones."} 
  ]},
  {q:"Escenario 10: Líquidos peligrosos sin gafas.", emoji:"⚗️👀", options:[
    {text:"Usar gafas cuando hay riesgo de proyección de líquidos", type:"correct", feedback:"¡Bien! Protege tus ojos."}, 
    {text:"Mirar de reojo sin gafas", type:"incorrect", feedback:"Riesgo de lesiones."}, 
    {text:"Cubrirse parcialmente", type:"partial", feedback:"No es suficiente."} 
  ]}
];

let current=0;
let score=0;
let ranking=[];

function showQuestion(){
  const quizDiv=document.getElementById('quiz');
  quizDiv.innerHTML="";
  if(current<questions.length){
    const q=questions[current];
    const qDiv=document.createElement('div');
    qDiv.className='question';
    qDiv.innerHTML=`<h2>${q.q}</h2><div class="emoji">${q.emoji}</div>`;
    const optionsDiv=document.createElement('div');
    optionsDiv.className='options';
    q.options.forEach(opt=>{
      const btn=document.createElement('button');
      btn.textContent=opt.text;
      btn.onclick=()=>handleAnswer(opt.type,opt.feedback,btn);
      optionsDiv.appendChild(btn);
    });
    qDiv.appendChild(optionsDiv);
    quizDiv.appendChild(qDiv);
    updateProgress();
  } else showScore();
}

function handleAnswer(type,feedback,btn){
  if(type==='correct') score+=10;
  else if(type==='partial') score+=5;

  btn.parentNode.querySelectorAll('button').forEach(b=>{
    b.disabled=true;
    if(b!==btn) b.style.opacity=0.6;
  });
  btn.style.opacity=1;

  const feedbackDiv=document.createElement('div');
  feedbackDiv.className='feedback show';
  feedbackDiv.textContent=feedback;
  btn.parentNode.appendChild(feedbackDiv);

  setTimeout(nextQuestion,1500);
}

function nextQuestion(){ current++; showQuestion(); }

function showScore(){
  const quizDiv=document.getElementById('quiz');
  ranking.push(score);
  ranking.sort((a,b)=>b-a);
  quizDiv.innerHTML=`<div class="score">¡Has completado el simulador!<br>Tu puntuación: ${score} / ${questions.length*10}</div>
                     <div class="ranking">Top puntuaciones: ${ranking.slice(0,5).join(" - ")}</div>
                     <div style="margin-top:15px;">Revisa los escenarios donde obtuviste menos puntos y refuerza las medidas de seguridad. ¡La prevención salva vidas!</div>`;
}

function updateProgress(){
  const progressBar=document.getElementById('progress');
  progressBar.style.width=((current/questions.length)*100)+'%';
}

showQuestion();
</script>
</body>
</html>
