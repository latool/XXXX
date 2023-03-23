# 🔍 [nlpTool]

This is an NLP-based LA Tool that offers the possibility to automatically:

- read literature from pdf
- identify keywords from text
- categorize the text based on keywords

![Fig3 SystemArc](https://user-images.githubusercontent.com/91469248/226555819-8b9be44b-704f-40fe-9681-fbb26bfdaece.jpg)

[nlpTool] is an NLP-based ML extension for [LaTool] developed in Python (backend) and Streamlit4 (frontend) that extracts the content from a Portable Document Format (PDF) article and further identifies and categorizes keywords (LD-LA Instruments) from the extracted content based on instruments specified in its classifier. Figure above presents the working and system architecture of [nlpTool] with our [LaTool], where the DB consists of manually harvested LA indicators and their metrics from LA literature and is aligned with LD events and activities presented in [LaTool].

Figure above presents the working and system architecture of [nlpTool] with our [LaTool], where the DB consists of manually harvested LA indicators and their metrics from LA literature and is aligned with LD events and activities presented in [LaTool].

[nlpTool] has four main functionalities: text extraction, preprocessing, training the classifier, and displaying the extracted and processed results out of new (untagged) documents (see above Figure). First, [nlpTool] reads the PDF articles and extracts the content/text from them, and converts them into pickle format (a Python object serialization that converts an object into a byte stream). We used the same articles and their features (LD-LA Instruments) as [2Blinded] which consists of 161 LA publications and their corresponding manually extracted and already aligned instruments i.e. LD events, LD-LA activities, LA indicators and metrics. Second, preprocessing is performed which includes four steps: 1. The extracted text is tokenized (to a list of words). 2. The stopwords that do not contain any meaning (e.g., the, it, etc.) and special characters and numbers are removed. 3. The words are converted to their base form by removing affixes called stemming (e.g., eating, eats, eaten to eat). [nlpTool] offers two stemming algorithms PorterStemmer (which works best with any type of word and the one we used for training) and WordNetLemmatizer (which works best with verbs and particularly nouns). 4. Finally in preprocessing, with the help of our dataset (Data.json) words are assigned labels to indicate their semantic role (relationship). Every article (pickle file) name contains a reference number and each of the LA instruments in the DB (Data.json) have the same reference number, which helps the NLP classifier learn while assigning the labels. Third, in [nlpTool] functionalities includes classifier training, where the tool checks for certain instruments/words from the DB in the extracted content/text and learns from it. The NLP classifier is trained using the Naive Bayes algorithm, a supervised
ML learning algorithm mainly used for solving text classification problems that include a multi-dimensional training dataset, which is suitable for our [LaTool] dataset.

Lastly, for displaying results, the user provides an unknown LA article the tool extracts, processes, and reads its content and with the help of the classifiers, the predicted results are shown to the user, where the ‘prediction’ value is a log-likelihood threshold (calculated by the NLP classifier, which indicates the accuracy of the result) the closer it is to zero the higher the accuracy (see Figure below).

![Fig4_Results](https://user-images.githubusercontent.com/91469248/226553770-7b41e509-71f4-4c97-9535-f080e059b4b9.png)


## Getting Started with [nlpTool]

1. The first step is to create the project folder. The name is not important therefore you can name it as you like. I will use the name `main`.
   
   - In the main folder you have to create a folder `pdfs` with a subfolder `extracted text`.
   - In the main folder you have to create a folder `classifier` with subfolders `event`, `activity`, `indicator` and `metric`.

2. Now put the files into the correct location:
   
   - The pdf files have to be inside the folder `main/pdfs/` and **need the correct structure with the citation nr in square brackets in front and a whitespace after it (e.g. [42] xyz.pdf)!** This is very important otherwise the training can't be done correctly.
   - The `data.json` file has to be inside the folder `main/`.

3. Now you can clone the repository into a subfolder e.g. `[nlpTool]` by 
   
   ```bash
   git clone git@github.com:latool/XXXX.git
   ```

4. When you're done with first 3 steps your project structure will look like this:

```bash
main
+-- [nlpTool]
+-- pdfs
    [1] foo.pdf
    [69] bar.pdf
    ...
    +-- extracted_text
+-- classifier
    +-- event
    +-- activity
    +-- indicator
    +-- metric
data.json
```

5. Navigate into the repository and install the necessary libraries:
   
   ```bash
   pip install -r requirements.txt
   ```

6. Run this command (to install the necessary nltk files):
   
   ```bash
   python nltk_files.py
   ```

If done correctly your browser should automatically run and navigate to `http://localhost:8501/?page=start` (if not then you can do it manually).

Now you should see this page:

![image-20220519121811141](/img/tut_landing_page.png)

## Train a classifier

To train a classifier click on `Exercise` in the navigation bar on the left. You should see this page:

![image-20220519122304588](/img/tut_training.png)

There you have three checkboxes:

- Extract text: This will read the pdf files (in `main/pdfs/`), extract their texts and save them into `main/pdfs/extracted_text/` as pickle files
- Label text: This will access the previously extracted texts (in `main/pdfs/extracted_text/`) and assign a label on every text. After that, all the labeled files are saved according to the label type (e.g. `main/classifier/event/` for label type `event`).
  - You can choose different setups here (generally you get best results by checking `Remove Stopwords?` and selecting `PorterStemmer`)
  - For label types please select all four!
- Run Training: This will run a training by accessing the labeled literature and *learning* how the features and the labels correlate.
  - You can choose different types of labels to train. **Attention:** Training a classifier is a complex task and therefore might take a while (especially when training a `metric`-classifier, it might take up to 30 minutes!) 

#### Next steps

Please do the following tasks:

1. Check both `Extract text` and `Label text` and click on `Start`:

![image-20220519123814125](/img/tut_labeling.png)

2. After this is finished, click on `Run Training` (you have to uncheck the other two options first), choose `event` and click on start:

![image-20220519124634520](/img/tut_run_training.png)

3. Now rerun step 2 again for every other label type (`metric` might take up to 30 minutes!)

🎉 Congratulations! You have trained 4 different classifiers and can now predict `events`, `activities`, `indicators` and even `metrics` of new pdfs!

## Predict labels of new pdf files

Choose `Extract` in the navigation bar and you should be see this screen:

![image-20220519124429056](/img/tut_predict.png)

To predict labels of new pdf files:

1. choose `Upload PDF` and either by drag and drop  `Browse files`. You should see an information below the upload area  about the uploaded file.
2. Now select all the label types you want to extract.
3. Click on Extract. 

Your output should look like this (Depends on what you chose in step 2):

![image-20220519125119392](/img/tut_extraction_output.png)

This shows the most probable label with a prediction value. This number represents the distance of the assigned label to the learned feature-label pairs of the classifier. Therefore a value closer to 0.0 is better. The cut off here is -45.0 to provide a variety of different labels.

## Misc

Choose `Examine` to dive into the data and see some interesting statistics. 

## ⚙ Technology

Python `3.10.1`

pdfplumber `0.6.2`
streamlit `1.5.0`
nltk `3.7`
