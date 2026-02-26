# solar-clean-vosges

<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Solar Clean Vosges - Devis Client</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<style>
body {font-family: Arial; background:#f2f5f4; margin:0; padding:20px;}
.container {max-width:750px; margin:auto; background:#fff; padding:30px; border-radius:12px; box-shadow:0 0 20px rgba(0,0,0,0.08);}
h1 {text-align:center; color:#0b6e4f; margin-bottom:5px;}
.subtitle {text-align:center; color:#666; margin-bottom:30px;}
h3 {margin-top:25px; color:#0b6e4f;}
label {font-weight:bold; display:block; margin-top:15px;}
input {width:100%; padding:10px; margin-top:5px; border-radius:6px; border:1px solid #ccc;}
button {width:100%; padding:14px; background:#0b6e4f; color:white; border:none; font-size:16px; border-radius:8px; cursor:pointer; margin-top:15px;}
button:hover {background:#09543c;}
.result {margin-top:30px; background:#e8f5f0; padding:20px; border-radius:10px;}
.total {font-size:22px; font-weight:bold; margin-top:10px;}
.legal {font-size:13px; color:#555; margin-top:15px;}
</style>
</head>
<body>
<div class="container">
<h1>☀ Solar Clean Vosges</h1>
<div class="subtitle">Devis client</div>

<h3>Vos informations</h3>
<label>Nom</label>
<input type="text" id="client">
<label>Adresse</label>
<input type="text" id="adresse">
<label>Téléphone</label>
<input type="text" id="telephone">

<h3>Détails de l'intervention</h3>
<label>Nombre de panneaux</label>
<input type="number" id="panneaux">
<label>Prix par panneau (€)</label>
<input type="number" id="prix" value="9">
<label>Frais de déplacement (€)</label>
<input type="number" id="deplacement" value="20">
<label>Suppléments (hauteur, accès difficile…) (€)</label>
<input type="number" id="supplement" value="0">
<label>Remise (%)</label>
<input type="number" id="remise" value="0">

<button onclick="genererDevis()">Générer le devis</button>
<button onclick="exportPDF()">Exporter en PDF</button>

<div class="result" id="resultat"></div>
</div>

<script>
let compteur = 1;
let dernierDevis = {};

function genererDevis() {
    let client = document.getElementById("client").value;
    let adresse = document.getElementById("adresse").value;
    let telephone = document.getElementById("telephone").value;
    let panneaux = parseFloat(document.getElementById("panneaux").value) || 0;
    let prix = parseFloat(document.getElementById("prix").value) || 0;
    let deplacement = parseFloat(document.getElementById("deplacement").value) || 0;
    let supplement = parseFloat(document.getElementById("supplement").value) || 0;
    let remise = parseFloat(document.getElementById("remise").value) || 0;

    let sousTotal = (panneaux * prix) + deplacement + supplement;
    let reduction = sousTotal * (remise / 100);
    let total = sousTotal - reduction;

    let numero = "SCV-CLIENT-" + String(compteur).padStart(3, '0');
    compteur++;

    dernierDevis = { numero, client, adresse, telephone, panneaux, prix, deplacement, supplement, remise, total };

    document.getElementById("resultat").innerHTML = `
        <h3>Devis N° ${numero}</h3>
        <p><strong>Entreprise :</strong> Solar Clean Vosges - Theo Delorme</p>
        <p><strong>Client :</strong> ${client}</p>
        <p><strong>Adresse :</strong> ${adresse}</p>
        <p><strong>Téléphone :</strong> ${telephone}</p>
        <hr>
        <p>Panneaux : ${panneaux} x ${prix} €</p>
        <p>Déplacement : ${deplacement} €</p>
        <p>Suppléments : ${supplement} €</p>
        <p>Remise : ${remise}%</p>
        <hr>
        <div class="total">Total : ${total.toFixed(2)} €</div>
        <div class="legal">TVA non applicable – article 293 B du CGI<br>Devis valable 30 jours</div>
    `;
}

function exportPDF() {
    if (!dernierDevis.numero) { alert("Génère d'abord le devis !"); return; }

    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    let y=20;
    doc.setFontSize(18); doc.text("☀ Solar Clean Vosges",105,y,{align:"center"}); y+=10;
    doc.setFontSize(12); doc.text("Theo Delorme - Micro-entreprise",105,y,{align:"center"}); y+=15;
    doc.setFontSize(14);
    doc.text(`Devis N°: ${dernierDevis.numero}`,20,y); y+=10;
    doc.setFontSize(12);
    doc.text(`Client: ${dernierDevis.client}`,20,y); y+=7;
    doc.text(`Adresse: ${dernierDevis.adresse}`,20,y); y+=7;
    doc.text(`Téléphone: ${dernierDevis.telephone}`,20,y); y+=10;
    doc.text(`Panneaux: ${dernierDevis.panneaux} x ${dernierDevis.prix} €`,20,y); y+=7;
    doc.text(`Déplacement: ${dernierDevis.deplacement} €`,20,y); y+=7;
    doc.text(`Suppléments: ${dernierDevis.supplement} €`,20,y); y+=7;
    doc.text(`Remise: ${dernierDevis.remise}%`,20,y); y+=10;
    doc.setFontSize(16); doc.text(`Total: ${dernierDevis.total.toFixed(2)} €`,20,y); y+=10;
    doc.setFontSize(10);
    doc.text("TVA non applicable – article 293 B du CGI",20,y); y+=5;
    doc.text("Devis valable 30 jours",20,y);
    doc.save(`${dernierDevis.numero}.pdf`);
}
</script>
</body>
</html>
