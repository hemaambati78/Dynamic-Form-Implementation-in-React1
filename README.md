import React, { useState, useEffect } from 'react';

const DynamicForm = () => {
  // Step 1: Define state variables
  const [formFields, setFormFields] = useState([]);
  const [formData, setFormData] = useState({});
  const [selectedForm, setSelectedForm] = useState('');
  const [progress, setProgress] = useState(0);
  const [error, setError] = useState('');

  // Simulate API responses
  const apiResponses = {
    userInformation: {
      fields: [
        { name: 'firstName', type: 'text', label: 'First Name', required: true },
        { name: 'lastName', type: 'text', label: 'Last Name', required: true },
        { name: 'age', type: 'number', label: 'Age', required: false },
      ]
    },
    addressInformation: {
      fields: [
        { name: 'street', type: 'text', label: 'Street', required: true },
        { name: 'city', type: 'text', label: 'City', required: true },
        { name: 'state', type: 'dropdown', label: 'State', options: ['California', 'Texas', 'New York'], required: true },
        { name: 'zipCode', type: 'text', label: 'Zip Code', required: false },
      ]
    },
    paymentInformation: {
      fields: [
        { name: 'cardNumber', type: 'text', label: 'Card Number', required: true },
        { name: 'expiryDate', type: 'date', label: 'Expiry Date', required: true },
        { name: 'cvv', type: 'password', label: 'CVV', required: true },
        { name: 'cardholderName', type: 'text', label: 'Cardholder Name', required: true },
      ]
    }
  };

  // Step 2: Handle form selection
  const handleFormSelection = (event) => {
    setSelectedForm(event.target.value);
    setError('');
    setFormData({});
    setProgress(0);
  };

  // Step 3: Fetch form fields based on selection
  useEffect(() => {
    if (selectedForm && apiResponses[selectedForm]) {
      setFormFields(apiResponses[selectedForm].fields);
    }
  }, [selectedForm]);

  // Step 4: Handle input change
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData({
      ...formData,
      [name]: value
    });
    calculateProgress();
  };

  // Step 5: Calculate form progress
  const calculateProgress = () => {
    let filledFields = 0;
    formFields.forEach(field => {
      if (field.required && formData[field.name]) {
        filledFields += 1;
      }
    });
    setProgress((filledFields / formFields.length) * 100);
  };

  // Step 6: Form submission
  const handleSubmit = (e) => {
    e.preventDefault();
    let valid = true;
    let errors = [];

    formFields.forEach(field => {
      if (field.required && !formData[field.name]) {
        valid = false;
        errors.push(`${field.label} is required`);
      }
    });

    if (valid) {
      alert('Form submitted successfully!');
      console.log(formData);
    } else {
      setError(errors.join(', '));
    }
  };

  // Step 7: Render dynamic fields
  const renderFields = () => {
    return formFields.map((field, index) => {
      switch (field.type) {
        case 'text':
        case 'number':
        case 'date':
        case 'password':
          return (
            <div key={index} className="form-group">
              <label>{field.label}</label>
              <input
                type={field.type}
                name={field.name}
                value={formData[field.name] || ''}
                onChange={handleChange}
                required={field.required}
              />
            </div>
          );
        case 'dropdown':
          return (
            <div key={index} className="form-group">
              <label>{field.label}</label>
              <select
                name={field.name}
                value={formData[field.name] || ''}
                onChange={handleChange}
                required={field.required}
              >
                <option value="">Select {field.label}</option>
                {field.options.map((option, idx) => (
                  <option key={idx} value={option}>
                    {option}
                  </option>
                ))}
              </select>
            </div>
          );
        default:
          return null;
      }
    });
  };

  // Step 8: Display progress bar and form content
  return (
    <div className="form-container">
      <header>
        <h1>Dynamic Form</h1>
      </header>
      <form onSubmit={handleSubmit}>
        <div className="form-group">
          <label>Choose Form</label>
          <select onChange={handleFormSelection} value={selectedForm}>
            <option value="">Select Form Type</option>
            <option value="userInformation">User Information</option>
            <option value="addressInformation">Address Information</option>
            <option value="paymentInformation">Payment Information</option>
          </select>
        </div>

        {selectedForm && (
          <>
            {renderFields()}
            {error && <div className="error-message">{error}</div>}
            <div className="progress-bar-container">
              <progress value={progress} max="100"></progress>
              <span>{Math.round(progress)}% completed</span>
            </div>
            <button type="submit">Submit</button>
          </>
        )}
      </form>

      <footer>
        <p>Form Footer - Designed by You</p>
      </footer>
    </div>
  );
};

export default DynamicForm;
