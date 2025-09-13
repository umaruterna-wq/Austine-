<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>StoryBeatz</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #ff416c, #ff4b2b);
      margin: 0;
      padding: 0;
      color: #fff;
      text-align: center;
    }
    header {
      padding: 20px;
      background: rgba(0, 0, 0, 0.3);
    }
    h1 { margin: 0; font-size: 2.5em; }
    .upload-section {
      margin: 40px auto;
      padding: 20px;
      background: rgba(255, 255, 255, 0.1);
      border-radius: 10px;
      width: 90%;
      max-width: 400px;
    }
    input[type="file"] { margin: 10px 0; }
    button {
      background: #ff4b2b;
      border: none;
      padding: 10px 20px;
      border-radius: 5px;
      color: #fff;
      font-size: 1em;
      cursor: pointer;
    }
    button:hover { background: #ff1c1c; }
    .icon { font-size: 50px; margin-bottom: 10px; }
    progress {
      width: 100%;
      margin-top: 10px;
    }
  </style>
</head>
<body>

  <header>
    <h1><i class="fa-solid fa-music"></i> StoryBeatz</h1>
    <p>Share your beats and stories with the world 🌍</p>
  </header>

  <div class="upload-section">
    <i class="fa-solid fa-cloud-arrow-up icon"></i>
    <h2>Upload Your Track</h2>
    <input type="file" id="fileInput" accept="audio/*"><br>
    <button onclick="uploadFile()"><i class="fa-solid fa-upload"></i> Upload</button>
    <p id="status"></p>
    <progress id="progressBar" value="0" max="100" style="display:none;"></progress>
  </div>

  <!-- Firebase SDKs -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getStorage, ref, uploadBytesResumable, getDownloadURL } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js";
    import { getFirestore, collection, addDoc, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

    // 🔥 Replace with your Firebase config
    const firebaseConfig =// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyDwigu-FicNiRJegp1b1ufjCEvT8uSWUDs",
  authDomain: "storybeatz-d76cb.firebaseapp.com",
  projectId: "storybeatz-d76cb",
  storageBucket: "storybeatz-d76cb.firebasestorage.app",
  messagingSenderId: "828777206248",
  appId: "1:828777206248:web:e5ddfd40f47842dfe9145d",
  measurementId: "G-KRXKHMQPRZ"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);
    const storage = getStorage(app);
    const db = getFirestore(app);

    async function uploadFile() {
      const file = document.getElementById("fileInput").files[0];
      const status = document.getElementById("status");
      const progressBar = document.getElementById("progressBar");

      if (!file) {
        alert("Please select a file first!");
        return;
      }

      status.innerText = "Uploading... ⏳";
      progressBar.style.display = "block";
      progressBar.value = 0;

      const storageRef = ref(storage, "tracks/" + file.name);
      const uploadTask = uploadBytesResumable(storageRef, file);

      uploadTask.on('state_changed',
        (snapshot) => {
          // Update progress
          const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
          progressBar.value = progress;
        },
        (error) => {
          console.error(error);
          status.innerText = "❌ Upload failed!";
          progressBar.style.display = "none";
        },
        async () => {
          // Upload complete
          const url = await getDownloadURL(uploadTask.snapshot.ref);
          await addDoc(collection(db, "tracks"), {
            name: file.name,
            url: url,
            createdAt: serverTimestamp()
          });
          status.innerText = "✅ Upload successful!";
          progressBar.style.display = "none";
        }
      );
    }
  </script>
</body>
</html>
