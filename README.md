# No-Code-Generator-Website
APP.js
import React, { useState } from 'react';
import axios from 'axios';

function App() {
  const [formData, setFormData] = useState({
    projectName: '',
    service: '',
    components: [],
  });

  const [errors, setErrors] = useState({});
  const [message, setMessage] = useState('');

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;

    if (type === 'checkbox') {
      setFormData((prev) => ({
        ...prev,
        components: checked
          ? [...prev.components, value]
          : prev.components.filter((c) => c !== value),
      }));
    } else {
      setFormData((prev) => ({ ...prev, [name]: value }));
    }

    setErrors((prev) => ({ ...prev, [name]: '' }));
    setMessage('');
  };

  const validateForm = () => {
    let validationErrors = {};
    if (!formData.projectName.trim()) validationErrors.projectName = 'Project name is required.';
    if (!formData.service.trim()) validationErrors.service = 'Service type is required.';
    if (formData.components.length === 0) validationErrors.components = 'Select at least one component.';
    setErrors(validationErrors);
    return Object.keys(validationErrors).length === 0;
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!validateForm()) return;

    try {
      await axios.post('http://localhost:5000/api/form', formData);
      setMessage('✅ Form submitted successfully!');
      setFormData({ projectName: '', service: '', components: [] });
      setErrors({});
    } catch (err) {
      console.error(err);
      setMessage('❌ Failed to submit the form.');
    }
  };

  return (
    <div style={styles.container}>
      <h2 style={styles.title}>No Code Website Generator</h2>

      {message && (
        <div style={message.startsWith('✅') ? styles.successMessage : styles.errorMessage}>
          {message}
        </div>
      )}

      <form onSubmit={handleSubmit} style={styles.form}>

        <label style={styles.label}>Project Name:</label>
        <input
          type="text"
          name="projectName"
          value={formData.projectName}
          onChange={handleChange}
          style={styles.input}
          placeholder="Enter project name"
        />
        {errors.projectName && <span style={styles.errorText}>{errors.projectName}</span>}

        <label style={styles.label}>Service Type:</label>
        <select
          name="service"
          value={formData.service}
          onChange={handleChange}
          style={styles.select}
        >
          <option value="">Select a service</option>
          <option value="ecommerce">E-Commerce</option>
          <option value="portfolio">Portfolio</option>
          <option value="blog">Blog</option>
          <option value="landing">Landing Page</option>
        </select>
        {errors.service && <span style={styles.errorText}>{errors.service}</span>}

        <fieldset style={styles.fieldset}>
          <legend style={styles.legend}>Components:</legend>
          {["Header", "Footer", "Gallery", "Contact Form"].map((comp) => (
            <label key={comp} style={styles.checkboxLabel}>
              <input
                type="checkbox"
                name="components"
                value={comp}
                checked={formData.components.includes(comp)}
                onChange={handleChange}
                style={styles.checkbox}
              />
              {comp}
            </label>
          ))}
          {errors.components && <span style={styles.errorText}>{errors.components}</span>}
        </fieldset>

        <button type="submit" style={styles.button}>Submit</button>
      </form>
    </div>
  );
}

const styles = {
  container: {
    maxWidth: 500,
    margin: '40px auto',
    padding: 30,
    boxShadow: '0 0 15px rgba(0,0,0,0.1)',
    borderRadius: 10,
    fontFamily: "'Segoe UI', Tahoma, Geneva, Verdana, sans-serif",
    backgroundColor: '#fff',
  },
  title: {
    textAlign: 'center',
    marginBottom: 25,
    color: '#333',
  },
  form: {
    display: 'flex',
    flexDirection: 'column',
  },
  label: {
    marginBottom: 6,
    fontWeight: '600',
    color: '#444',
  },
  input: {
    padding: 10,
    marginBottom: 10,
    fontSize: 16,
    borderRadius: 5,
    border: '1px solid #ccc',
  },
  select: {
    padding: 10,
    marginBottom: 10,
    fontSize: 16,
    borderRadius: 5,
    border: '1px solid #ccc',
    backgroundColor: '#fff',
  },
  fieldset: {
    border: '1px solid #ddd',
    borderRadius: 5,
    padding: 15,
    marginBottom: 15,
  },
  legend: {
    fontWeight: '600',
    marginBottom: 10,
    color: '#555',
  },
  checkboxLabel: {
    display: 'block',
    marginBottom: 8,
    fontSize: 15,
    color: '#333',
  },
  checkbox: {
    marginRight: 8,
  },
  button: {
    backgroundColor: '#007bff',
    color: '#fff',
    border: 'none',
    padding: '12px 20px',
    fontSize: 16,
    borderRadius: 6,
    cursor: 'pointer',
    fontWeight: '600',
  },
  errorText: {
    color: '#e74c3c',
    fontSize: 13,
    marginBottom: 10,
  },
  successMessage: {
    backgroundColor: '#d4edda',
    color: '#155724',
    padding: 10,
    borderRadius: 5,
    marginBottom: 20,
    textAlign: 'center',
  },
  errorMessage: {
    backgroundColor: '#f8d7da',
    color: '#721c24',
    padding: 10,
    borderRadius: 5,
    marginBottom: 20,
    textAlign: 'center',
  },
};

export default App;

projectForm.js
import React, { useState } from 'react';
import axios from 'axios';

function ProjectForm() {
  const [formData, setFormData] = useState({
    projectName: '',
    service: '',
    components: [],
  });

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    if (type === 'checkbox') {
      setFormData((prev) => ({
        ...prev,
        components: checked
          ? [...prev.components, value]
          : prev.components.filter((c) => c !== value),
      }));
    } else {
      setFormData((prev) => ({ ...prev, [name]: value }));
    }
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const response = await axios.post('http://localhost:5000/api/projects', formData);
      alert('Project submitted successfully!');
      console.log('Response:', response.data);
    } catch (err) {
      console.error('Submission error:', err);
      alert('Project submission failed.');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>Project Name:</label><br />
      <input
        type="text"
        name="projectName"
        onChange={handleChange}
        required
      /><br /><br />

      <label>Service Type:</label><br />
      <select name="service" onChange={handleChange} required>
        <option value="">Select a service</option>
        <option value="ecommerce">E-Commerce</option>
        <option value="portfolio">Portfolio</option>
        <option value="blog">Blog</option>
        <option value="landing">Landing Page</option>
      </select><br /><br />

      <label>Components:</label><br />
      {["Header", "Footer", "Gallery", "Contact Form"].map((comp) => (
        <label key={comp} style={{ marginRight: '10px' }}>
          <input
            type="checkbox"
            name="components"
            value={comp}
            onChange={handleChange}
          />
          {comp}
        </label>
      ))}
      <br /><br />

      <button type="submit">Submit</button>
    </form>
  );
}

export default ProjectForm;


index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
