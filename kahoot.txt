var Version = '1.0.25';
var questions = [];
var info = {
    numQuestions: 0,
    questionNum: -1,
    lastAnsweredQuestion: -1,
    defaultIL: true,
    ILSetQuestion: -1
};
var PPT = 950;
var Answered_PPT = 950;
var autoAnswer = false;
var showAnswers = false;
var inputLag = 100;

function FindByAttributeValue(attribute, value, element_type) {
    element_type = element_type || "*";
    var All = document.getElementsByTagName(element_type);
    for (var i = 0; i < All.length; i++) {
        if (All[i].getAttribute(attribute) == value) { return All[i]; }
    }
    return null;
}

// --- Intro Overlay ---
const introOverlay = document.createElement("div");
introOverlay.style.position = "fixed";
introOverlay.style.top = "0";
introOverlay.style.left = "0";
introOverlay.style.width = "100vw";
introOverlay.style.height = "100vh";
introOverlay.style.background = "linear-gradient(135deg, #1e0034, #0d0019)";
introOverlay.style.display = "flex";
introOverlay.style.justifyContent = "center";
introOverlay.style.alignItems = "center";
introOverlay.style.zIndex = "10000";
introOverlay.style.flexDirection = "column";
introOverlay.style.color = "white";
introOverlay.style.fontFamily = '"Montserrat", sans-serif';
introOverlay.style.textAlign = "center";
introOverlay.style.transition = "opacity 1s ease";

const introTitle = document.createElement("h1");
introTitle.textContent = "🌲 Forest Kahoot Mod 🌲";
introTitle.style.fontSize = "3vw";
introTitle.style.marginBottom = "1vw";
introTitle.style.textShadow = "0px 0px 25px rgba(0,255,200,0.7)";
introOverlay.appendChild(introTitle);

const introSubtitle = document.createElement("p");
introSubtitle.textContent = "Clean Interface • Auto Answer • Quiz Controls";
introSubtitle.style.fontSize = "1.3vw";
introSubtitle.style.opacity = "0.9";
introOverlay.appendChild(introSubtitle);

document.body.appendChild(introOverlay);
setTimeout(() => {
    introOverlay.style.opacity = "0";
    setTimeout(() => document.body.removeChild(introOverlay), 1000);
}, 2500);

// --- UI Window ---
const uiElement = document.createElement("div");
uiElement.className = "floating-ui";
uiElement.style.position = "absolute";
uiElement.style.top = "5%";
uiElement.style.left = "5%";
uiElement.style.width = "33vw";
uiElement.style.height = "auto";
uiElement.style.backgroundColor = "#381272";
uiElement.style.borderRadius = "1vw";
uiElement.style.boxShadow = "0px 0px 10px 0px rgba(0,0,0,0.5)";
uiElement.style.zIndex = "9999";
uiElement.style.transition = "all 0.3s ease";

// --- Handle ---
const handle = document.createElement("div");
handle.className = "handle";
handle.style.fontFamily = '"Montserrat", "Noto Sans Arabic", "Helvetica Neue", Helvetica, Arial, sans-serif"';
handle.style.fontSize = "1.5vw";
handle.textContent = "Forest kahoot hack!";
handle.style.color = "white";
handle.style.width = "97.5%";
handle.style.height = "2.5vw";
handle.style.backgroundColor = "#321066";
handle.style.borderRadius = "1vw 1vw 0 0";
handle.style.cursor = "grab";
handle.style.textAlign = "left";
handle.style.paddingLeft = "2.5%";
handle.style.lineHeight = "2vw";
uiElement.appendChild(handle);

// Close & Minimize buttons
const closeButton = document.createElement("div");
closeButton.textContent = "✕";
closeButton.style.position = "absolute";
closeButton.style.top = "0";
closeButton.style.right = "0";
closeButton.style.width = "12.5%";
closeButton.style.height = "2.5vw";
closeButton.style.backgroundColor = "red";
closeButton.style.color = "white";
closeButton.style.display = "flex";
closeButton.style.justifyContent = "center";
closeButton.style.alignItems = "center";
closeButton.style.cursor = "pointer";
handle.appendChild(closeButton);

const minimizeButton = document.createElement("div");
minimizeButton.textContent = "─";
minimizeButton.style.color = "white";
minimizeButton.style.position = "absolute";
minimizeButton.style.top = "0";
minimizeButton.style.right = "12.5%";
minimizeButton.style.width = "12.5%";
minimizeButton.style.height = "2.5vw";
minimizeButton.style.backgroundColor = "gray";
minimizeButton.style.display = "flex";
minimizeButton.style.justifyContent = "center";
minimizeButton.style.alignItems = "center";
minimizeButton.style.cursor = "pointer";
handle.appendChild(minimizeButton);

// --- Content ---
const contentContainer = document.createElement("div");
contentContainer.style.padding = "1vw";
uiElement.appendChild(contentContainer);

// Quiz ID
const quizHeader = document.createElement("h2");
quizHeader.textContent = "QUIZ ID";
quizHeader.style.color = "white";
quizHeader.style.textAlign = "center";
contentContainer.appendChild(quizHeader);

const inputBox = document.createElement("input");
inputBox.type = "text";
inputBox.placeholder = "Enter Quiz ID...";
inputBox.style.width = "90%";
inputBox.style.padding = "0.5vw";
inputBox.style.borderRadius = "0.7vw";
inputBox.style.border = "1px solid rgba(255,255,255,0.4)";
inputBox.style.outline = "none";
inputBox.style.background = "rgba(255,255,255,0.2)";
inputBox.style.color = "white";
inputBox.style.textAlign = "center";
contentContainer.appendChild(inputBox);

// Points Slider
const pointsLabel = document.createElement("span");
pointsLabel.textContent = "Points per Question: 950";
pointsLabel.style.color = "white";
pointsLabel.style.display = "block";
pointsLabel.style.textAlign = "center";
contentContainer.appendChild(pointsLabel);

const pointsSlider = document.createElement("input");
pointsSlider.type = "range";
pointsSlider.min = "500";
pointsSlider.max = "1000";
pointsSlider.value = "950";
pointsSlider.style.width = "90%";
pointsSlider.style.display = "block";
pointsSlider.style.margin = "0.5vw auto";
pointsSlider.style.cursor = "pointer";
contentContainer.appendChild(pointsSlider);

pointsSlider.addEventListener("input", () => {
    PPT = +pointsSlider.value;
    pointsLabel.textContent = "Points per Question: " + PPT;
});

// Auto Answer / Show Answers
function createSwitch(labelText, onChange) {
    const container = document.createElement("div");
    container.style.display = "flex";
    container.style.alignItems = "center";
    container.style.justifyContent = "center";
    container.style.margin = "0.5vw 0";

    const label = document.createElement("span");
    label.textContent = labelText;
    label.style.color = "white";
    label.style.marginRight = "1vw";
    container.appendChild(label);

    const labelSwitch = document.createElement("label");
    labelSwitch.className = "switch";

    const input = document.createElement("input");
    input.type = "checkbox";
    input.addEventListener("change", onChange);

    const slider = document.createElement("span");
    slider.className = "slider";

    labelSwitch.appendChild(input);
    labelSwitch.appendChild(slider);
    container.appendChild(labelSwitch);
    contentContainer.appendChild(container);
}

createSwitch("Auto Answer", function () {
    autoAnswer = this.checked;
    info.ILSetQuestion = info.questionNum;
});

createSwitch("Show Answers", function () {
    showAnswers = this.checked;
});

// Info Labels
const infoLabel = document.createElement("h2");
infoLabel.textContent = "INFO";
infoLabel.style.color = "white";
infoLabel.style.textAlign = "center";
contentContainer.appendChild(infoLabel);

const questionsLabel = document.createElement("span");
questionsLabel.textContent = "Question 0 / 0";
questionsLabel.style.color = "white";
questionsLabel.style.display = "block";
questionsLabel.style.textAlign = "center";
contentContainer.appendChild(questionsLabel);

const inputLagLabel = document.createElement("span");
inputLagLabel.textContent = "Input lag: 125 ms";
inputLagLabel.style.color = "white";
inputLagLabel.style.display = "block";
inputLagLabel.style.textAlign = "center";
contentContainer.appendChild(inputLagLabel);

// Version & GitHub
const versionLabel = document.createElement("span");
versionLabel.textContent = "Forest Kahoot Mod V" + Version;
versionLabel.style.color = "white";
versionLabel.style.display = "block";
versionLabel.style.textAlign = "center";
versionLabel.style.marginTop = "1vw";
contentContainer.appendChild(versionLabel);

const githubLink = document.createElement("a");
githubLink.href = "https://github.com/forestvrreal";
githubLink.target = "_blank";
githubLink.textContent = "GitHub: Forest";
githubLink.style.color = "white";
githubLink.style.display = "block";
githubLink.style.textAlign = "center";
contentContainer.appendChild(githubLink);

// --- Switch Styles ---
const style = document.createElement("style");
style.textContent = `
.switch {position: relative; display: inline-block; width: 4.5vw; height: 2.2vw;}
.switch input {display:none;}
.slider {position: absolute; cursor: pointer; background:#ff4d4d; border-radius:2vw; top:0; left:0; right:0; bottom:0; transition:.4s;}
.slider:before {content:""; position:absolute; width:1.8vw; height:1.8vw; left:0.2vw; bottom:0.2vw; background:white; border-radius:50%; transition:.4s;}
input:checked + .slider {background:#4caf50;}
input:checked + .slider:before {transform:translateX(2.2vw);}
`;
document.head.appendChild(style);

// --- UI Functions ---
closeButton.addEventListener("click", () => {
    document.body.removeChild(uiElement);
    autoAnswer = false;
    showAnswers = false;
});

let isMinimized = false;
minimizeButton.addEventListener("click", () => {
    isMinimized = !isMinimized;
    contentContainer.style.display = isMinimized ? "none" : "block";
    uiElement.style.height = isMinimized ? "2.5vw" : "auto";
});

// --- Drag Perfect Follow ---
let isDragging = false;
let offsetX = 0, offsetY = 0;
let raf = null;

handle.addEventListener("mousedown", e => {
    isDragging = true;
    const rect = uiElement.getBoundingClientRect();
    offsetX = e.clientX - rect.left;
    offsetY = e.clientY - rect.top;

    // Ensure the element can move
    if (window.getComputedStyle(uiElement).position === "static") {
        uiElement.style.position = "absolute";
    }
});

document.addEventListener("mousemove", e => {
    if (!isDragging) return;

    const x = e.clientX - offsetX;
    const y = e.clientY - offsetY;

    if (!raf) {
        raf = requestAnimationFrame(() => {
            uiElement.style.left = x + "px";
            uiElement.style.top = y + "px";
            raf = null;
        });
    }
});

document.addEventListener("mouseup", () => {
    isDragging = false;
});



// --- Questions Parsing ---
function parseQuestions(questionsJson) {
    return questionsJson.map(question => {
        let q = { type: question.type, time: question.time, answers: [], incorrectAnswers: [] };
        if (['quiz', 'multiple_select_quiz'].includes(question.type)) {
            question.choices.forEach((choice, i) => {
                if (choice.correct) q.answers.push(i);
                else q.incorrectAnswers.push(i);
            });
        } else if (question.type == 'open_ended') {
            question.answers = question.choices.map(c => c.answer);
        }
        return q;
    });
}

// --- Handle Quiz ID Input ---
function handleInputChange() {
    const quizID = inputBox.value;
    if (!quizID) { inputBox.style.backgroundColor = "white"; info.numQuestions = 0; return; }
    fetch(`https://kahoot.it/rest/kahoots/${quizID}`)
        .then(res => { if (!res.ok) throw new Error(""); return res.json(); })
        .then(data => {
            inputBox.style.backgroundColor = "green";
            questions = parseQuestions(data.questions);
            info.numQuestions = questions.length;
        }).catch(() => { inputBox.style.backgroundColor = "red"; info.numQuestions = 0; });
}
inputBox.addEventListener("input", handleInputChange);

document.body.appendChild(uiElement);

// --- Question Handling ---
function highlightAnswers(question) {
    question.answers.forEach(a => { const el = FindByAttributeValue("data-functional-selector", "answer-" + a, "button"); if (el) el.style.backgroundColor = "rgb(0,255,0)"; });
    question.incorrectAnswers.forEach(a => { const el = FindByAttributeValue("data-functional-selector", "answer-" + a, "button"); if (el) el.style.backgroundColor = "rgb(255,0,0)"; });
}

function answer(question, time) {
    Answered_PPT = PPT;
    const delay = question.type === 'multiple_select_quiz' ? 60 : 0;
    setTimeout(() => {
        if (question.type === 'quiz') {
            const key = (+question.answers[0] + 1).toString();
            window.dispatchEvent(new KeyboardEvent('keydown', { key }));
        } else if (question.type === 'multiple_select_quiz') {
            question.answers.forEach(a => {
                const key = (+a + 1).toString();
                window.dispatchEvent(new KeyboardEvent('keydown', { key }));
            });
            const submit = FindByAttributeValue("data-functional-selector", "multi-select-submit-button", "button");
            if (submit) submit.click();
        }
    }, time - delay);
}

function onQuestionStart() {
    const question = questions[info.questionNum];
    if (!question) return;
    if (showAnswers) highlightAnswers(question);
    if (autoAnswer) answer(question, (question.time - question.time / (500 / (PPT - 500))) - inputLag);
}

// --- Update Loop ---
setInterval(() => {
    const textElement = FindByAttributeValue("data-functional-selector", "question-index-counter", "div");
    if (textElement) info.questionNum = +textElement.textContent - 1;

    if (FindByAttributeValue("data-functional-selector", "answer-0", "button") && info.lastAnsweredQuestion != info.questionNum) {
        info.lastAnsweredQuestion = info.questionNum;
        onQuestionStart();
    }

    // Auto input lag adjustment
    if (autoAnswer && info.ILSetQuestion != info.questionNum) {
        let ppt = Answered_PPT > 987 ? 1000 : Answered_PPT;
        const incrementElement = FindByAttributeValue("data-functional-selector", "score-increment", "span");
        if (incrementElement) {
            info.ILSetQuestion = info.questionNum;
            const increment = +incrementElement.textContent.split(" ")[1];
            if (increment != 0) inputLag = Math.round(inputLag + (ppt - increment) * 15);
        }
    }

    questionsLabel.textContent = `Question ${info.questionNum + 1} / ${info.numQuestions}`;
    inputLagLabel.textContent = `Input lag: ${inputLag} ms`;
}, 1);
