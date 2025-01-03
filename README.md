# django-forms-frontend-validation App
___

This project provides a comprehensive system for handling and validating HTML forms in Django applications. It combines client-side JavaScript-based validation and server-side Python logic for robust form processing.

The application is designed to streamline the process of form validation, ensuring user inputs meet the requirements before submitting them to the server. The system offers features like automatic required-field validation, error handling, and dynamic CSRF token management for secure data transmission.

## Features
- Client-Side Validation: 
  - Automatically validates required fields.
  - Displays validation errors inline and dynamically updates them upon correction.
  - Adds asterisks to labels of required fields for better user clarity.
- Server-Side Settings:
  - Control which forms and fields to ignore during validation.
  - Define validation behavior, such as enforcing checks only on form submission.
- Integration with Django Settings:
  - Use Django's settings to dynamically configure validation rules.
- Secure Fetch Calls:
  - Includes CSRF token management for secure AJAX-based form submissions.

## Usage
### Setting Up
1. Include Required Components:
   - Include the JavaScript files in your project to enable client-side functionality.
   - Ensure `form_utils.py` is imported and accessible in your Django app.
2. Define Settings in settings.py
   - Add `formvalidator` to installed apps.
      ```python
      INSTALLED_APPS = [
        ...,
        'formvalidator',
      ]
      ```
   - Configure the following variables to customize the behavior, after importing the form settings.
      ```python
     from formvalidator.settings import * 
     
     IGNORED_CLASSES = ['example-class', 'example-class-2', ...]
      IGNORE_VALIDATION = ['example-ignore-validation', ...]
      VALIDATE_ONLY_ON_SUBMIT = ['all']  # Options: "__all__", specific class names, or leave empty.
      ```
3. Initial Forms:
   - Ensure the `_InitializeForms` method is called during page load to attach validation logic to forms dynamically.
   To your HTML template with the form, add this.
   ```html
   <script src="{% static 'dist/forms.bundle.js' %}"></script> 
   <script>
    // fv (formsvalidator) is exported from forms.bundle.js
    window.addEventListener("load", () => {
        let ignoredClasses = {{ form_validator.ignored_classes|safe }}; // add more classes that represent forms you want this script to ignore.
        let ignoreValidation = {{ form_validator.ignore_validation|safe }}; // add any form classes that you want to ignore validation
        let validateOnlyOnSubmit = {{ form_validator.validate_only_on_submit|safe }}; // for hitting all forms make index 0 either __all__, all, * or leave blank for false or use false
        let forms = document.getElementsByTagName("form");
        // if (form || userForm) {
        if (forms.length > 0) {
            // calling specific functions on all forms
            fv._InitializeForms(forms, ignoredClasses, ignoreValidation, validateOnlyOnSubmit);
        }
    });
   </script>
   ```
4. Server-Side Context:
   - Use the `FormsValidator` class to pass configuration to templates:
   ```python
    from formvalidator.form_utils import FormsValidator
   
   
    def my_view(request):
        form_validator = FormsValidator()
        context = {
            'form_validator': form_validator.get_context(),
        }    
        return render(request, 'my_template.html', context)
   ```