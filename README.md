# Survey Form Builder (ReactJS)
## Date: 12-05-2025

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

export default function SurveyApp() {
  const [mode, setMode] = useState('build');
  const [questions, setQuestions] = useState([]);
  const [newQuestion, setNewQuestion] = useState({ text: '', type: 'text', options: '' });
  const [responses, setResponses] = useState({});
  const [submitted, setSubmitted] = useState(false);

  // Add question to list
  const addQuestion = () => {
    if (!newQuestion.text.trim()) return;
    const id = Date.now();
    const question = {
      id,
      text: newQuestion.text,
      type: newQuestion.type,
      options: newQuestion.type !== 'text' ? newQuestion.options.split(',').map(o => o.trim()) : [],
    };
    setQuestions([...questions, question]);
    setNewQuestion({ text: '', type: 'text', options: '' });
  };

  const deleteQuestion = (id) => {
    setQuestions(questions.filter(q => q.id !== id));
  };

  const handleChangeResponse = (id, value) => {
    setResponses({ ...responses, [id]: value });
  };

  const handleCheckboxChange = (id, option) => {
    const current = responses[id] || [];
    const updated = current.includes(option)
      ? current.filter(o => o !== option)
      : [...current, option];
    setResponses({ ...responses, [id]: updated });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    setSubmitted(true);
  };

  return (
    <div style={{ padding: '20px', fontFamily: 'Arial' }}>
      <h2>Dynamic Survey Form Builder</h2>
      <button onClick={() => {
        setMode(mode === 'build' ? 'fill' : 'build');
        setSubmitted(false);
        setResponses({});
      }}>
        Switch to {mode === 'build' ? 'Fill' : 'Build'} Mode
      </button>

      {/* Build Mode */}
      {mode === 'build' && (
        <div style={{ marginTop: '20px' }}>
          <h3>Add Question</h3>
          <input
            type="text"
            placeholder="Question Text"
            value={newQuestion.text}
            onChange={(e) => setNewQuestion({ ...newQuestion, text: e.target.value })}
          />
          <select
            value={newQuestion.type}
            onChange={(e) => setNewQuestion({ ...newQuestion, type: e.target.value })}
          >
            <option value="text">Text</option>
            <option value="radio">Radio</option>
            <option value="checkbox">Checkbox</option>
          </select>
          {(newQuestion.type === 'radio' || newQuestion.type === 'checkbox') && (
            <input
              type="text"
              placeholder="Options (comma separated)"
              value={newQuestion.options}
              onChange={(e) => setNewQuestion({ ...newQuestion, options: e.target.value })}
            />
          )}
          <button onClick={addQuestion}>Add</button>

          <ul>
            {questions.map(q => (
              <li key={q.id}>
                <strong>{q.text}</strong> ({q.type})
                {q.options.length > 0 && <span>: {q.options.join(', ')}</span>}
                <button onClick={() => deleteQuestion(q.id)} style={{ marginLeft: '10px' }}>Delete</button>
              </li>
            ))}
          </ul>
        </div>
      )}

      {/* Fill Mode */}
      {mode === 'fill' && (
        <form onSubmit={handleSubmit} style={{ marginTop: '20px' }}>
          <h3>Fill the Survey</h3>
          {questions.map(q => (
            <div key={q.id} style={{ marginBottom: '15px' }}>
              <label>{q.text}</label>
              <div>
                {q.type === 'text' && (
                  <input
                    type="text"
                    value={responses[q.id] || ''}
                    onChange={(e) => handleChangeResponse(q.id, e.target.value)}
                  />
                )}
                {q.type === 'radio' &&
                  q.options.map(opt => (
                    <label key={opt}>
                      <input
                        type="radio"
                        name={q.id}
                        value={opt}
                        checked={responses[q.id] === opt}
                        onChange={() => handleChangeResponse(q.id, opt)}
                      />
                      {opt}
                    </label>
                  ))}
                {q.type === 'checkbox' &&
                  q.options.map(opt => (
                    <label key={opt}>
                      <input
                        type="checkbox"
                        value={opt}
                        checked={(responses[q.id] || []).includes(opt)}
                        onChange={() => handleCheckboxChange(q.id, opt)}
                      />
                      {opt}
                    </label>
                  ))}
              </div>
            </div>
          ))}
          {questions.length > 0 && <button type="submit">Submit</button>}
        </form>
      )}

      {/* Summary */}
      {submitted && (
        <div style={{ marginTop: '30px' }}>
          <h3>Survey Responses</h3>
          <ul>
            {questions.map(q => (
              <li key={q.id}>
                <strong>{q.text}:</strong>{' '}
                {Array.isArray(responses[q.id])
                  ? responses[q.id].join(', ')
                  : responses[q.id] || 'N/A'}
              </li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
}

```

## OUTPUT
![Screenshot 2025-05-12 103847](https://github.com/user-attachments/assets/e2e03607-a492-4e9f-ae13-fe2cb122fd1b)

![Screenshot 2025-05-12 103918](https://github.com/user-attachments/assets/51678c48-2ba9-4973-8814-8b6c7ac39fd3)

## RESULT
The program for creating Survey Form Builder using ReactJS is executed successfully.
