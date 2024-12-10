# useBlocker

## MyForm Component

```
import React, { useState } from 'react';
import { useForm } from 'react-hook-form';
import { useBlocker } from 'react-router-dom';
import { useNavigate } from 'react-router-dom';
import './MyForm.css'; // Importing CSS for styling

const MyForm = () => {
  const navigate = useNavigate();
  const { register, handleSubmit, formState: { isDirty, errors } } = useForm({
    defaultValues: { name: "", email: "" }
  });

  const [alertModal, setAlertModal] = useState({ show: false, title: "", content: "" });
  const [proceed, setProceed] = useState(false);
  const [navigateTo, setNavigateTo] = useState(null);

  const onSubmit = (data) => {
    console.log("Form submitted:", data);
    // Reset the form or perform any other action after submission
  };

  const handleNavigation = ({ nextLocation }) => {
    if (isDirty) {
      setProceed(true);
      setNavigateTo(nextLocation.pathname);
      setAlertModal({
        show: true,
        title: "You have unsaved changes.",
        content: "All unsaved changes will be lost.",
      });
      return false; // Prevent navigation
    }
    return true; // Allow navigation
  };

  useBlocker(handleNavigation);

  const handleAlertConfirm = () => {
    setProceed(false);
    setAlertModal({ show: false, title: "", content: "" });
    if (navigateTo) {
      navigate(navigateTo); // Navigate to the next location
    }
  };

  return (
    <div>
      {alertModal.show && (
        <div className="alert-modal">
          <h2>{alertModal.title}</h2>
          <p>{alertModal.content}</p>
          <button onClick={handleAlertConfirm}>Proceed</button>
          <button onClick={() => setAlertModal({ show: false })}>Cancel</button>
        </div>
      )}
      <form onSubmit={handleSubmit(onSubmit)}>
        <div>
          <input 
            {...register('name', { required: "Name is required" })} 
            placeholder="Name" 
          />
          {errors.name && <p className="error">{errors.name.message}</p>}
        </div>
        <div>
          <input 
            {...register('email', { 
              required: "Email is required", 
              pattern: {
                value: /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/,
                message: "Invalid email address"
              }
            })} 
            placeholder="Email" 
          />
          {errors.email && <p className="error">{errors.email.message}</p>}
        </div>
        <button type="submit">Submit</button>
      </form>
    </div>
  );
};

export default MyForm;

```

## CSS for Modal (MyForm.css)

```
.alert-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  border: 1px solid #ccc;
  padding: 20px;
  z-index: 1000;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.alert-modal h2 {
  margin: 0 0 10px;
}

.alert-modal p {
  margin: 0 0 20px;
}

.error {
  color: red;
  font-size: 0.8em;
}

```
