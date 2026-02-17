<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>BluVader Empire Production Studio</title>

<!-- External Libraries -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/docx/8.0.3/docx.umd.js"></script>

<style>
body{
    margin:0;
    font-family:Arial, sans-serif;
    background:#0f172a;
    color:white;
}
header{
    background:#111827;
    padding:20px;
    text-align:center;
    font-size:22px;
    font-weight:bold;
}
nav{
    display:flex;
    background:#1f2937;
}
nav button{
    flex:1;
    padding:15px;
    border:none;
    background:#1f2937;
    color:white;
    cursor:pointer;
    font-weight:bold;
}
nav button:hover{
    background:#374151;
}
section{
    display:none;
    padding:20px;
}
.active{
    display:block;
}
input, textarea, select{
    width:100%;
    padding:10px;
    margin:8px 0;
    border-radius:6px;
    border:none;
    box-sizing:border-box;
}
button.primary{
    background:#2563eb;
    padding:10px;
    border:none;
    border-radius:6px;
    color:white;
    cursor:pointer;
}
button.primary:hover{
    background:#1d4ed8;
}
.output-box{
    background:#111827;
    padding:15px;
    border-radius:8px;
    height:300px;
    overflow:auto;
    white-space:pre-wrap;
}
</style>
</head>

<body>

<header>🚀 BluVader Empire Production Studio</header>

<nav>
<button onclick="showTab('create')">Create</button>
<button onclick="showTab('rewrite')">Rewrite</button>
<button onclick="showTab('cover')">Cover</button>
<button onclick="showTab('export')">Export</button>
<button onclick="showTab('promote')">Promote</button>
</nav>

<!-- CREATE TAB -->
<section id="create" class="active">
<h3>Create Content</h3>

<input type="password" id="apiKey" placeholder="Enter OpenAI API Key">

<select id="mode">
<option value="book">Book Generator</option>
<option value="course">Course Creator</option>
<option value="script">YouTube Script</option>
<option value="sales">Sales Page</option>
<option value="email">Email Sequence</option>
</select>

<input type="text" id="topic" placeholder="Enter Topic">
<button class="primary" onclick="generateContent()">Generate</button>

<div class="output-box" id="output"></div>
</section>

<!-- REWRITE TAB -->
<section id="rewrite">
<h3>Upload & Rewrite</h3>
<input type="file" id="fileUpload">
<select id="rewriteMode">
<option value="improve">Improve Writing</option>
<option value="ebook">Convert to Ebook</option>
<option value="script">Convert to Script</option>
<option value="course">Convert to Course</option>
</select>
<button class="primary" onclick="rewriteContent()">Rewrite</button>
<div class="output-box" id="rewriteOutput"></div>
</section>

<!-- COVER TAB -->
<section id="cover">
<h3>AI Cover Generator</h3>
<input type="text" id="coverPrompt" placeholder="Describe your book cover">
<button class="primary" onclick="generateCover()">Generate Cover</button>
<br><br>
<img id="coverImage" style="max-width:300px;">
</section>

<!-- EXPORT TAB -->
<section id="export">
<h3>Export Options</h3>
<button class="primary" onclick="downloadTXT()">Download TXT</button>
<button class="primary" onclick="downloadHTML()">Download HTML</button>
<button class="primary" onclick="downloadEPUB()">Download EPUB</button>
<button class="primary" onclick="downloadDOCX()">Download DOCX</button>
</section>

<!-- PROMOTE TAB -->
<section id="promote">
<h3>Promotion Generator</h3>
<select id="promoMode">
<option value="tiktok">TikTok Hooks</option>
<option value="youtube">YouTube Description</option>
<option value="instagram">Instagram Caption</option>
<option value="affiliate">Affiliate Email Funnel</option>
</select>
<button class="primary" onclick="generatePromo()">Generate Promo</button>
<div class="output-box" id="promoOutput"></div>
</section>

<script>

function showTab(tab){
document.querySelectorAll("section").forEach(sec=>sec.classList.remove("active"));
document.getElementById(tab).classList.add("active");
}

async function callAI(prompt){
const key = document.getElementById("apiKey").value;
const response = await fetch("https://api.openai.com/v1/chat/completions",{
method:"POST",
headers:{
"Content-Type":"application/json",
"Authorization":"Bearer "+key
},
body:JSON.stringify({
model:"gpt-4o",
messages:[{role:"user",content:prompt}],
temperature:0.7
})
});
const data = await response.json();
return data.choices[0].message.content;
}

async function generateContent(){
const mode = document.getElementById("mode").value;
const topic = document.getElementById("topic").value;

let prompt = "";

if(mode==="book") prompt=`Write a complete structured ebook about ${topic}`;
if(mode==="course") prompt=`Create a detailed online course about ${topic}`;
if(mode==="script") prompt=`Write a YouTube video script about ${topic}`;
if(mode==="sales") prompt=`Write a high converting sales page for ${topic}`;
if(mode==="email") prompt=`Write a 5 email marketing sequence for ${topic}`;

const result = await callAI(prompt);
document.getElementById("output").innerText=result;
}

async function rewriteContent(){
const file=document.getElementById("fileUpload").files[0];
const mode=document.getElementById("rewriteMode").value;
if(!file)return alert("Upload file first");
const text=await file.text();
let prompt=`Rewrite the following text in ${mode} format:\n\n${text}`;
const result=await callAI(prompt);
document.getElementById("rewriteOutput").innerText=result;
}

async function generateCover(){
const prompt=document.getElementById("coverPrompt").value;
const key=document.getElementById("apiKey").value;

const response=await fetch("https://api.openai.com/v1/images/generations",{
method:"POST",
headers:{
"Content-Type":"application/json",
"Authorization":"Bearer "+key
},
body:JSON.stringify({
model:"gpt-image-1",
prompt:prompt,
size:"1024x1792"
})
});
const data=await response.json();
document.getElementById("coverImage").src=data.data[0].url;
}

function downloadTXT(){
const text=document.getElementById("output").innerText;
const blob=new Blob([text],{type:"text/plain"});
const a=document.createElement("a");
a.href=URL.createObjectURL(blob);
a.download="BluVader_Product.txt";
a.click();
}

function downloadHTML(){
const text=document.getElementById("output").innerText;
const blob=new Blob([`<pre>${text}</pre>`],{type:"text/html"});
const a=document.createElement("a");
a.href=URL.createObjectURL(blob);
a.download="BluVader_Product.html";
a.click();
}

async function downloadEPUB(){
const zip=new JSZip();
zip.file("mimetype","application/epub+zip");
zip.file("content.xhtml",`<html><body><pre>${document.getElementById("output").innerText}</pre></body></html>`);
const content=await zip.generateAsync({type:"blob"});
const a=document.createElement("a");
a.href=URL.createObjectURL(content);
a.download="BluVader_Product.epub";
a.click();
}

async function downloadDOCX(){
const {Document,Packer,Paragraph}=docx;
const doc=new Document({
sections:[{
children:[new Paragraph(document.getElementById("output").innerText)]
}]
});
const blob=await Packer.toBlob(doc);
const a=document.createElement("a");
a.href=URL.createObjectURL(blob);
a.download="BluVader_Product.docx";
a.click();
}

async function generatePromo(){
const mode=document.getElementById("promoMode").value;
const topic=document.getElementById("topic").value;
let prompt=`Generate ${mode} content for ${topic}`;
const result=await callAI(prompt);
document.getElementById("promoOutput").innerText=result;
}

</script>

</body>
</html>
