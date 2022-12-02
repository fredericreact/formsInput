# Different kind of validation

![image](https://user-images.githubusercontent.com/104289891/205252127-3a9bd96a-65ad-4c86-92e3-696720218e9b.png)


```javascript
import { useState} from 'react'
 
const SimpleInput = (props) => {
 
 
  const [enteredName, setEnteredName] = useState('');
  const [enteredNameTouched, setEnteredNameTouched] = useState(false)
 
const enteredNameIsValid = enteredName.trim() !=='';
const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched
 
  const nameInputChangeHandler = event => {
    setEnteredName(event.target.value)
  }
 
  const nameInputBlurHandler = event => {
    setEnteredNameTouched(true);
  }
 
  const formSubmissionHandler = event => {
    event.preventDefault();
 
    setEnteredNameTouched(true)
 
    if (!enteredNameIsValid) {
      return;
    }
 
    console.log(enteredName)
 
    setEnteredName('')
    setEnteredNameTouched(false);
  }
 
 
  const nameInputClasses = nameInputIsInvalid ? 'form-control invalid' : 'form-control';
 
  return (
    <form onSubmit={formSubmissionHandler}>
      <div className={nameInputClasses}>
        <label htmlFor='name'>Your Name</label>
        <input
       
        type='text'
        id='name'
        onChange ={nameInputChangeHandler}
        onBlur = {nameInputBlurHandler}
        value={enteredName}
        />
        {nameInputIsInvalid && <p className='error-text'>Name must not be empty</p>}
      </div>
      <div className="form-actions">
        <button>Submit</button>
      </div>
    </form>
  );
};
 
export default SimpleInput;
```

Here we have 3 types of validation combined : 

1- If input submitted is empty : error (submit validation)

2- If input is touched then left empty : error (lose focus validation)

3- If input is in error state then filled : remove error (keystroke validation)


# Check form validity

TO check form validity, and thus that all inputs are valid , we could just do an if statement : 


```javascript
const enteredNameIsValid = enteredName.trim() !=='';
const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched
 
const enteredEmailIsValid = enteredEmail.includes('@');
const enteredEmailIsInvalid = !enteredEmailIsValid && enteredEmailTouched
 
let formIsValid = false
 
 
  if (enteredNameIsValid && enteredEmailIsValid) {
    formIsValid = true;
  }  

```

# Custom Hook (Input)

Before :

```javascript
import {useState} from 'react'
 
const SimpleInput = (props) => {
 
 
  const [enteredName, setEnteredName] = useState('');
  const [enteredNameTouched, setEnteredNameTouched] = useState(false)
  const [enteredEmail, setEnteredEmail] = useState('')
  const [enteredEmailTouched, setEnteredEmailTouched] = useState(false)
 
const enteredNameIsValid = enteredName.trim() !=='';
const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched
 
const enteredEmailIsValid = enteredEmail.includes('@');
const enteredEmailIsInvalid = !enteredEmailIsValid && enteredEmailTouched
 
let formIsValid = false
 
 
  if (enteredNameIsValid && enteredEmailIsValid) {
    formIsValid = true;
  }  
 
  const nameInputChangeHandler = event => {
    setEnteredName(event.target.value)
  }
 
  const emailInputChangeHandler = event => {
    setEnteredEmail(event.target.value);
  }
 
  const nameInputBlurHandler = event => {
    setEnteredNameTouched(true);
  }
 
  const emailInputBlurHandler = event => {
    setEnteredEmailTouched(true)
  }
 
  const formSubmissionHandler = event => {
    event.preventDefault();
 
    setEnteredNameTouched(true)
 
    if (!enteredNameIsValid) {
      return;
    }
 
    console.log(enteredName)
 
    setEnteredName('')
    setEnteredNameTouched(false);
    setEnteredEmail('')
    setEnteredEmailTouched(false)
  }
 
 
  const nameInputClasses = nameInputIsInvalid ? 'form-control invalid' : 'form-control';
 
  const emailInputClasses = enteredEmailIsInvalid ? 'form-control invalid' : 'form-control';
 
  return (
    <form onSubmit={formSubmissionHandler}>
      <div className={nameInputClasses}>
        <label htmlFor='name'>Your Name</label>
        <input
       
        type='text'
        id='name'
        onChange ={nameInputChangeHandler}
        onBlur = {nameInputBlurHandler}
        value={enteredName}
        />
        {nameInputIsInvalid && <p className='error-text'>Name must not be empty</p>}
      </div>
 
      <div className={emailInputClasses}>
        <label htmlFor='email'>Your Email</label>
        <input
       
        type='email'
        id='email'
        onChange ={emailInputChangeHandler}
        onBlur = {emailInputBlurHandler}
        value={enteredEmail}
        />
        {enteredEmailIsInvalid && <p className='error-text'>Please enter valid email</p>}
      </div>
 
      <div className="form-actions">
        <button disabled={!formIsValid}>Submit</button>
      </div>
    </form>
  );
};
 
export default SimpleInput;
```

After :

```javascript
import {useState} from 'react'
 
const useInput = (validateValue) => {
    const [enteredValue, setEnteredValue] = useState('');
    const [isTouched, setIsTouched] = useState(false)
 
 
    const valueIsValid = validateValue(enteredValue);
const hasError = !valueIsValid && isTouched
 
const valueChangeHandler = event => {
    setEnteredValue(event.target.value)
  }
 
  const inputBlurHandler = event => {
    setIsTouched(true);
  }
 
  const reset = () => {
    setEnteredValue('');
    setIsTouched(false)
  }
 
    return {
        value: enteredValue,
        isValid: valueIsValid,
        hasError,
        valueChangeHandler,
        inputBlurHandler,
        reset
    }
 
}
 
export default useInput;
```

```javascript
import useInput from '../hooks/use-Input'
 
const SimpleInput = (props) => {
 
  const {
    value:enteredName,
    isValid: enteredNameIsValid,
    hasError: nameInputHasError,
    valueChangeHandler: nameChangeHandler,
    inputBlurHandler: nameBlurHandler,
    reset: resetNameInput
  } = useInput(value => value.trim() !=='');
 
  const {
    value:enteredEmail,
    isValid: enteredEmailIsValid,
    hasError: emailInputHasError,
    valueChangeHandler: emailChangeHandler,
    inputBlurHandler: emailBlurHandler,
    reset: resetEmailInput
  } = useInput(value => value.includes('@'));
 
let formIsValid = false
 
 
  if (enteredNameIsValid && enteredEmailIsValid) {
    formIsValid = true;
  }  
 
  const formSubmissionHandler = event => {
    event.preventDefault();
 
 
 
    if (!enteredNameIsValid) {
      return;
    }
 
    console.log(enteredName)
 
    resetNameInput()
    resetEmailInput()
  }
 
 
  const nameInputClasses = nameInputHasError ? 'form-control invalid' : 'form-control';
 
  const emailInputClasses = emailInputHasError ? 'form-control invalid' : 'form-control';
 
  return (
    <form onSubmit={formSubmissionHandler}>
      <div className={nameInputClasses}>
        <label htmlFor='name'>Your Name</label>
        <input
       
        type='text'
        id='name'
        onChange ={nameChangeHandler}
        onBlur = {nameBlurHandler}
        value={enteredName}
        />
        {nameInputHasError && <p className='error-text'>Name must not be empty</p>}
      </div>
 
      <div className={emailInputClasses}>
        <label htmlFor='email'>Your Email</label>
        <input
       
        type='email'
        id='email'
        onChange ={emailChangeHandler}
        onBlur = {emailBlurHandler}
        value={enteredEmail}
        />
        {emailInputHasError && <p className='error-text'>Please enter valid email</p>}
      </div>
 
      <div className="form-actions">
        <button disabled={!formIsValid}>Submit</button>
      </div>
    </form>
  );
};
 
export default SimpleInput;
```

# reuse the custom Hook for a new form 


```javascript
import useInput from '../hooks/use-input';
 
const isNotEmpty = value => value.trim() !=='';
const isEmail = (value) => value.includes('@');
 
 
const BasicForm = (props) => {
 
  const {
    value: firstNameValue,
    isValid: firstNameIsValid,
    hasError: firstNameHasError,
    valueChangeHandler: firstNameChangeHandler,
    inputBlurHandler: firstNameBlurHandler,
    reset: resetFirstName,
  } = useInput(isNotEmpty);
 
  const {
    value: lastNameValue,
    isValid: lastNameIsValid,
    hasError: lastNameHasError,
    valueChangeHandler: lastNameChangeHandler,
    inputBlurHandler: lastNameBlurHandler,
    reset: resetLastName,
  } = useInput(isNotEmpty);
 
  const {
    value: emailValue,
    isValid: emailIsValid,
    hasError: emailHasError,
    valueChangeHandler: emailChangeHandler,
    inputBlurHandler: emailBlurHandler,
    reset: resetEmail,
  } = useInput(isEmail);
 
  let formIsValid = false;
 
  if (firstNameIsValid && lastNameIsValid && emailIsValid) {
    formIsValid = true;
  }
 
  const submitHandler = event => {
    event.preventDefault();
 
    if (!formIsValid) {
      return;
    }
 
    console.log('Submitted!');
    console.log(firstNameValue, lastNameValue, emailValue);
 
    resetFirstName();
    resetLastName();
    resetEmail();
  };
 
  const firstNameClasses = firstNameHasError ? 'form-control invalid' : 'form-control';
  const lastNameClasses = lastNameHasError ? 'form-control invalid' : 'form-control';
  const emailClasses = emailHasError ? 'form-control invalid' : 'form-control';
 
  return (
    <form onSubmit={submitHandler}>
      <div className='control-group'>
 
      <div className={firstNameClasses}>
          <label htmlFor='name'>First Name</label>
          <input
            type='text'
            id='name'
            value={firstNameValue}
            onChange={firstNameChangeHandler}
            onBlur={firstNameBlurHandler}
          />
          {firstNameHasError && <p className="error-text">Please enter a first name.</p>}
        </div>
 
        <div className={lastNameClasses}>
          <label htmlFor='name'>Last Name</label>
          <input
            type='text'
            id='name'
            value={lastNameValue}
            onChange={lastNameChangeHandler}
            onBlur={lastNameBlurHandler}
          />
          {lastNameHasError && <p className="error-text">Please enter a last name.</p>}
        </div>
 
      </div>
 
      <div className={emailClasses}>
        <label htmlFor='name'>E-Mail Address</label>
        <input
          type='text'
          id='name'
          value={emailValue}
          onChange={emailChangeHandler}
          onBlur={emailBlurHandler}
        />
        {emailHasError && <p className="error-text">Please enter a valid email address.</p>}
      </div>
 
      <div className='form-actions'>
      <button disabled={!formIsValid}>Submit</button>
      </div>
 
    </form>
  );
};
 
export default BasicForm;
```

# optional bonus not necessary : use usereducer() in the custom input hook instead of useState


```javascript
import {useReducer} from 'react'
 
const initialInputState = {
    value: '',
    isTouched: false,
  };
 
  const inputStateReducer = (state, action) => {
    if (action.type === 'INPUT') {
      return { value: action.value, isTouched: state.isTouched };
    }
    if (action.type === 'BLUR') {
      return { isTouched: true, value: state.value };
    }
    if (action.type === 'RESET') {
      return { isTouched: false, value: '' };
    }
    return inputStateReducer;
  };
 
 
const useInput = (validateValue) => {
   
    const [inputState, dispatch] = useReducer(
        inputStateReducer,
        initialInputState
      );
 
      const valueIsValid = validateValue(inputState.value);
      const hasError = !valueIsValid && inputState.isTouched;
 
const valueChangeHandler = event => {
    dispatch({ type: 'INPUT', value: event.target.value });
  }
 
  const inputBlurHandler = event => {
    dispatch({ type: 'BLUR' });
  }
 
  const reset = () => {
    dispatch({ type: 'RESET' });
  }
 
    return {
        value: inputState.value,
        isValid: valueIsValid,
        hasError,
        valueChangeHandler,
        inputBlurHandler,
        reset
    }
 
}
 
export default useInput;
```