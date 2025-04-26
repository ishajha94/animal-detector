<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Animal Detector</title>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@4.9.0/dist/tf.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/mobilenet@2.1.0"></script>
</head>
<body>
    <h1>Animal Detector</h1>
    <input type="file" id="imageInput" accept="image/*">
    <br><br>
    <img id="uploadedImage" width="300" style="display:none;"/>
    <h2 id="result">Please upload an image</h2>

    <script>
        let model;

        async function loadModel() {
            model = await mobilenet.load();
            console.log('Model loaded.');
        }

        loadModel();

        const imageInput = document.getElementById('imageInput');
        const uploadedImage = document.getElementById('uploadedImage');
        const result = document.getElementById('result');

        imageInput.addEventListener('change', async (event) => {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = async function(e) {
                uploadedImage.src = e.target.result;
                uploadedImage.style.display = 'block';

                // Wait for the image to load into the DOM
                uploadedImage.onload = async () => {
                    const predictions = await model.classify(uploadedImage);
                    console.log(predictions);

                    // Look for animal-related predictions
                    const animalPrediction = predictions.find(pred => 
                        pred.className.toLowerCase().match(/dog|cat|bird|horse|sheep|cow|elephant|bear|zebra|giraffe|animal/)
                    );

                    if (animalPrediction) {
                        result.innerText = `Detected: ${animalPrediction.className} (Confidence: ${(animalPrediction.probability * 100).toFixed(2)}%)`;
                    } else {
                        result.innerText = 'No animal detected confidently.';
                    }
                };
            }

            reader.readAsDataURL(file);
        });
    </script>
</body>
</html>
