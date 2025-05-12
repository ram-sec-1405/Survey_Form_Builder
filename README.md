# Survey Form Builder (ReactJS)
## Date:12/05/2025
## reg no:212223220086

## AIM
To create a Survey Form Builder using ReactJS..

## ALGORITHM
### STEP 1 Initialize States
mode ← 'build' (default mode)

questions ← [] (holds all survey questions)

currentQuestion ← { text: '', type: 'text', options: '' } (holds input while building)

editingIndex ← null (tracks which question is being edited)

responses ← {} (user responses to survey)

submitted ← false (indicates whether survey was submitted)

### STEP 2 Switch Modes
Toggle mode between 'build' and 'fill' when the toggle button is clicked.

Reset submitted to false when entering Fill Mode.

### STEP 3 Build Mode Logic
#### a. Add/Update Question
If currentQuestion.text is not empty:

If type is "radio" or "checkbox", split currentQuestion.options by commas → optionsArray.

Construct a questionObject:

If editingIndex is not null, replace question at that index.

Else, push questionObject into questions.

Reset currentQuestion and editingIndex.

#### b. Edit Question
Set currentQuestion to the selected question’s data.

Set editingIndex to the question’s index.

#### c. Delete Question
Remove the selected question from questions.

### STEP 4 Fill Mode Logic
#### a. Render Form
For each question in questions:

If type is "text", render a text input.

If type is "radio", render radio buttons for each option.

If type is "checkbox", render checkboxes.

#### b. Capture Responses
On input change:

For "text" and "radio", update responses[index] = value.

For "checkbox":

If option exists in responses[index], remove it.

Else, add it to the array.

### STEP 5 Submit Form
When user clicks "Submit":

Set submitted = true.

Display a summary using the questions and responses.

### STEP 6 Display Summary
Iterate over questions.

Display the response from responses[i] for each question:

If it's an array, join it with commas.

If empty, show "No response".


## PROGRAM

```
import React, { useState } from 'react';
import './App.css';

function App() {
  const [mode, setMode] = useState('build');
  const [questions, setQuestions] = useState([]);
  const [currentQuestion, setCurrentQuestion] = useState({ text: '', type: 'text', options: '' });
  const [editingIndex, setEditingIndex] = useState(null);
  const [responses, setResponses] = useState({});
  const [submitted, setSubmitted] = useState(false);

  const handleToggleMode = () => {
    setMode(mode === 'build' ? 'fill' : 'build');
    setSubmitted(false);
  };

  const handleAddOrUpdateQuestion = () => {
    if (!currentQuestion.text) return;

    const newQuestion = {
      ...currentQuestion,
      options: currentQuestion.type !== 'text'
        ? currentQuestion.options.split(',').map(opt => opt.trim())
        : [],
    };

    if (editingIndex !== null) {
      const updated = [...questions];
      updated[editingIndex] = newQuestion;
      setQuestions(updated);
    } else {
      setQuestions([...questions, newQuestion]);
    }

    setCurrentQuestion({ text: '', type: 'text', options: '' });
    setEditingIndex(null);
  };

  const handleEdit = (index) => {
    const q = questions[index];
    setCurrentQuestion({
      text: q.text,
      type: q.type,
      options: q.options.join(', ')
    });
    setEditingIndex(index);
  };

  const handleDelete = (index) => {
    const updated = questions.filter((_, i) => i !== index);
    setQuestions(updated);
  };

  const handleResponseChange = (index, value, option) => {
    if (questions[index].type === 'checkbox') {
      const current = responses[index] || [];
      const updated = current.includes(option)
        ? current.filter(o => o !== option)
        : [...current, option];
      setResponses({ ...responses, [index]: updated });
    } else {
      setResponses({ ...responses, [index]: value });
    }
  };

  const handleSubmit = () => {
    setSubmitted(true);
  };

  return (
    <div className="App">
      <h1>React Survey</h1>
      <button onClick={handleToggleMode}>
        Switch to {mode === 'build' ? 'Fill Mode' : 'Build Mode'}
      </button>

      {mode === 'build' ? (
        <div className="builder">
          <h2>Build Survey</h2>
          <input
            type="text"
            placeholder="Question text"
            value={currentQuestion.text}
            onChange={(e) => setCurrentQuestion({ ...currentQuestion, text: e.target.value })}
          />
          <select
            value={currentQuestion.type}
            onChange={(e) => setCurrentQuestion({ ...currentQuestion, type: e.target.value })}
          >
            <option value="text">Text</option>
            <option value="radio">Multiple Choice (Radio)</option>
            <option value="checkbox">Checkboxes</option>
          </select>
          {(currentQuestion.type === 'radio' || currentQuestion.type === 'checkbox') && (
            <input
              type="text"
              placeholder="Comma-separated options"
              value={currentQuestion.options}
              onChange={(e) => setCurrentQuestion({ ...currentQuestion, options: e.target.value })}
            />
          )}
          <button onClick={handleAddOrUpdateQuestion}>
            {editingIndex !== null ? 'Update' : 'Add'} Question
          </button>

          <ul>
            {questions.map((q, i) => (
              <li key={i}>
                <strong>{q.text}</strong> ({q.type})
                <button onClick={() => handleEdit(i)}>Edit</button>
                <button onClick={() => handleDelete(i)}>Delete</button>
              </li>
            ))}
          </ul>
        </div>
      ) : (
        <div className="filler">
          <h2>Fill Survey</h2>
          {questions.map((q, i) => (
            <div key={i} className="question-block">
              <p>{q.text}</p>
              {q.type === 'text' && (
                <input
                  type="text"
                  value={responses[i] || ''}
                  onChange={(e) => handleResponseChange(i, e.target.value)}
                />
              )}
              {q.type === 'radio' &&
                q.options.map((opt, j) => (
                  <label key={j}>
                    <input
                      type="radio"
                      name={question-${i}}
                      value={opt}
                      checked={responses[i] === opt}
                      onChange={() => handleResponseChange(i, opt)}
                    />
                    {opt}
                  </label>
                ))}
              {q.type === 'checkbox' &&
                q.options.map((opt, j) => (
                  <label key={j}>
                    <input
                      type="checkbox"
                      value={opt}
                      checked={responses[i]?.includes(opt)}
                      onChange={() => handleResponseChange(i, null, opt)}
                    />
                    {opt}
                  </label>
                ))}
            </div>
          ))}
          <button onClick={handleSubmit}>Submit</button>
          {submitted && (
            <div className="summary">
              <h3>Survey Summary</h3>
              <ul>
                {questions.map((q, i) => (
                  <li key={i}>
                    <strong>{q.text}</strong>: {Array.isArray(responses[i])
                      ? responses[i].join(', ')
                      : responses[i] || 'No response'}
                  </li>
                ))}
              </ul>
            </div>
          )}
        </div>
      )}
    </div>
  );
}

export default App; 
```


```
.App {
  font-family: sans-serif;
  padding: 1rem;
  max-width: 1250px;
  margin: auto;
  background: rgb(132, 190, 211);
}

input, select, button {
  margin: 1 rem 0;
  padding: 0.5rem;
  font-size: 1rem;
  display: block;
  width: 20%;
}

button {
  background-color: #333;
  color: white;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #555;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  margin-bottom: 1rem;
}

.question-block {
  margin-bottom: 1.5rem;
}

.summary {
  margin-top: 2rem;
  background: #f4f4f4;
  padding: 1rem;
  border-radius: 8px;
}
```
## OUTPUT

![WhatsApp Image 2025-05-12 at 15 44 17_0d8ae9e9](https://github.com/user-attachments/assets/fd352ea0-31d8-4eaf-b57f-79db078e35f2)


![WhatsApp Image 2025-05-12 at 15 44 17_22d70eca](https://github.com/user-attachments/assets/89b7518f-0832-42df-8a08-e2d678ed49b7)
![WhatsApp Image 2025-05-12 at 16 55 09_099a60e7](https://github.com/user-attachments/assets/660faac0-a141-4b40-875a-05687e119b0b)

## RESULT
The program for creating Survey Form Builder using ReactJS is executed successfully.
