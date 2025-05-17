<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Homework Submission</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background: #f4f4f4;
    }

    h2 {
      text-align: center;
    }

    video {
      width: 100%;
      max-width: 500px;
      border: 2px solid #333;
      margin-bottom: 10px;
    }

    #preview img {
      height: 100px;
      margin: 5px;
      border: 1px solid #ccc;
    }

    .form-group {
      margin: 10px 0;
    }

    select, button {
      padding: 10px;
      font-size: 16px;
      margin-bottom: 10px;
      width: 100%;
      max-width: 500px;
    }

    #buttons {
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
    }
  </style>
</head>
<body>

<h2>📸 Submit Homework</h2>

<video id="video" autoplay playsinline></video>
<div id="buttons">
  <button id="captureBtn">📷 Capture Image</button>
  <button id="makePDFBtn">📄 Create PDF</button>
  <button id="sendBtn" disabled>📧 Prepare Email</button>
</div>

<div id="preview"></div>

<div class="form-group">
  <label for="classSelect">Class</label>
  <select id="classSelect">
    <option value="">-- Select Class --</option>
    <option value="6">Class 6</option>
    <option value="7">Class 7</option>
    <option value="8">Class 8</option>
  </select>

  <label for="subjectSelect">Subject</label>
  <select id="subjectSelect">
    <option value="">-- Select Subject --</option>
    <option value="Math">Math</option>
    <option value="Science">Science</option>
    <option value="Sanskrit">Sanskrit</option>
  </select>

  <label for="chapterSelect">Chapter No</label>
  <select id="chapterSelect">
    <option value="">-- Select Chapter --</option>
    <option value="1">Chapter 1</option>
    <option value="2">Chapter 2</option>
    <option value="3">Chapter 3</option>
  </select>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script>
  const video = document.getElementById('video');
  const captureBtn = document.getElementById('captureBtn');
  const makePDFBtn = document.getElementById('makePDFBtn');
  const sendBtn = document.getElementById('sendBtn');
  const preview = document.getElementById('preview');
  let imageDataArray = [];
  let pdfBlobURL = "";

  // ✅ Open Camera
  navigator.mediaDevices.getUserMedia({ video: true })
    .then(stream => {
      video.srcObject = stream;
    })
    .catch(err => {
      alert("Error accessing camera. Please use HTTPS or localhost and allow camera access.");
      console.error("Camera error:", err);
    });

  // 📸 Capture Image
  captureBtn.onclick = () => {
    const canvas = document.createElement('canvas');
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
    canvas.getContext('2d').drawImage(video, 0, 0);

    const imageData = canvas.toDataURL('image/jpeg');
    imageDataArray.push(imageData);

    const img = document.createElement('img');
    img.src = imageData;
    preview.appendChild(img);
  };

  // 📄 Create PDF
  makePDFBtn.onclick = () => {
    if (imageDataArray.length === 0) {
      alert("Capture at least one image.");
      return;
    }

    const { jsPDF } = window.jspdf;
    const pdf = new jsPDF();

    imageDataArray.forEach((img, i) => {
      if (i > 0) pdf.addPage();
      pdf.addImage(img, 'JPEG', 10, 10, 190, 250);
    });

    const blob = pdf.output('blob');
    pdfBlobURL = URL.createObjectURL(blob);

    const a = document.createElement('a');
    a.href = pdfBlobURL;
    a.download = "homework.pdf";
    a.click();

    alert("PDF downloaded! You can now send it via email.");
    sendBtn.disabled = false;
  };

  // 📧 Prepare Mailto
  sendBtn.onclick = () => {
    const cls = document.getElementById('classSelect').value;
    const subject = document.getElementById('subjectSelect').value;
    const chapter = document.getElementById('chapterSelect').value;

    if (!cls || !subject || !chapter) {
      alert("Please select Class, Subject, and Chapter.");
      return;
    }

    const mailto = `mailto:homework.vardaan@gmail.com?subject=Homework Submission - Class ${cls} ${subject} Ch${chapter}&body=Dear Team,%0A%0APlease find attached the homework.%0AClass: ${cls}%0ASubject: ${subject}%0AChapter: ${chapter}%0A%0ARegards.`;

    window.location.href = mailto;
  };
</script>

</body>
</html>
