## Getting started with Cypress

### 1. Get a sandbox environment
	
- Clone the repository: `git clone https://github.com/bsipiak/simple_login_dockerised.git`

- Build and start environment (make sure docker is up and running): `make up`

- Our project will be available under: http://localhost:8080

### 2. Install cypress

- Install cypress in our project: `yarn add cypress --dev`
- Start cypress: `sudo yarn run cypress open`
- Change file permissions:
`sudo chown -R "$(whoami)" cypress`


### 3. First test!

- Create file `my_first_test.js` in folder cypress/integration
- Paste there this code: 	
```javascript
    describe('Go to main page', function () {
        it('should check if important elements are present on main page', function () {
            
            cy.visit('http://localhost:8080');
        
            cy.get('#inputUsername').should('exist');
            cy.get('#inputPassword').should('exist');
            cy.get(':checkbox[value="remember-me"]');
            cy.get('.btn').contains('Sign in');
        });
    });
```

### 4. Exercise I

Create test `my_second_test.js` test that will: 
1. login to our platform,
2. check if all elements in menu are present,
3. check if status says "logged in",
3. logout,
4. check if we are back on page 'http://localhost:8080/login.php'

#### Data
- Login: user
- Password: password

#### Hints
Check for should and url methods in  Cypress documentation:

https://docs.cypress.io/api/api/table-of-contents.html


<!--
```javascript
describe('Go to main page', function () {

    it('should check if important elements are present on main page', function () {

        cy.visit('http://localhost:8080');

        // Login
        cy.get('#inputUsername').type('user');
        cy.get('#inputPassword').type('password');
        cy.get('.btn').click();

        // Check if elements exists
        cy.get('.nav').contains('Home').should('exist');
        cy.get('.nav').contains('About').should('exist');
        cy.get('.nav').contains('Contact').should('exist');
        cy.get('.jumbotron').contains('logged in').should('exist');

        // Logout
        cy.get('.btn.btn-success').click();

        // Check URL
        cy.url().should('eq', 'http://localhost:8080/login.php')
    });
});

```
-->

### 5. Run tests from CLI
1. Exit cypress GUI
2. Delete all example tests created by Cypress as we don't need them
3. Start Cypress tests from terminal using `sudo cypress run`
4. Wait for results
5. Go to cypress/videos folder and check what was recorded by Cypress

### 6. Selectors
1. We will create selectors for elements on login page using good practices mentioned here: https://docs.cypress.io/guides/references/best-practices.html#Selecting-Elements
2. Notice that each element has atribute `data-cy`. This is selector insulated from CSS or JS that provides good context
3. Create new folder "selectors" inside cypress folder
4. Inside selectors folder create new file called login.js
5. Paste this piece of code there:

    ```javascript
    export default {
        selectors: {
            'username_input': '[data-cy=usermame]',
        }
    }
    ```
    In my_first_test.js file:
    - at the very top of file add `import { selectors as selectorsLoginPage } from '../selectors/login';`
    - change `cy.get('#inputUsername').should('exist');` into `cy.get(selectorsLoginPage.username_input).should('exist');`
6. Run tests again to validate
7. Challenge

    Refactor test from my_first_test.js to use selectors, target elements by `data-cy` attributes.
    As they will be used later in the course please name them `username_input`, `password_input`, `remember_me_checkbox`, `sign_in_button`

    <!--
    login.js
    ```javascript
    export default {
        selectors: {
            'username_input': '[data-cy=usermame]',
            'password_input': '[data-cy=password]',
            'remember_me_checkbox': '[data-cy=rememberMeCheckbox]',
            'sign_in_button': '[data-cy=signInButton]'
        }
    }
    ```
    
    my_first_test.js
    ```javascript
    import { selectors as selectorsLoginPage } from '../selectors/login';
    
    describe('Go to main page', function () {
        it('should check if important elements are present on main page', function () {
    
            cy.visit('http://localhost:8080');
    
            cy.get(selectorsLoginPage.username_input).should('exist');
            cy.get(selectorsLoginPage.password_input).should('exist');
            cy.get(selectorsLoginPage.remember_me_checkbox);
            cy.get(selectorsLoginPage.sign_in_button);
        });
    });
    ```
    -->

### 7. Exercise II
1. Create selectors file for site available after user logs in (index.php)
2. Add `data-cy` attributes to make site easily testable (optional)
3. Modify `my_second_test.js` to use selectors

### 8. Commands
1. Open file `cypress/support/commands.js`
2. Let's create a custom command that will:
    1. go to our login website
    2. type login credentials
    3. log in
    
    ```javascript
    import { selectors as selectorsLoginPage } from '../selectors/login';
    
    Cypress.Commands.add('login', function() {
        cy.get(selectorsLoginPage.username_input).type('user');
        cy.get(selectorsLoginPage.password_input).type('password');
        cy.get(selectorsLoginPage.sign_in_button).click();
    })
    ```
    
4. In `my_second_test.js` change login logic to this: `cy.login();` 
5. Run test to validate


### 9. Exercise III
1. Create 2 custom commands and use them in `my_second_test.js` to replace existing commands:
    1. check if menu contains all elements
    2. logout
    
    <!--   
    my_second_test.js.js
    ```javascript
    describe('Go to main page', function () {
    
        it('should check if important elements are present on main page', function () {
    
            cy.visit('http://localhost:8080');
            cy.login();
    
            cy.checkMenu();
            cy.get('.jumbotron').contains('logged in').should('exist');
    
            cy.logout();
            
            // Check URL
            cy.url().should('eq', 'http://localhost:8080/login.php')
        });
    });
    ```
    
    commands.js
    ```javascript
    Cypress.Commands.add('checkMenu', function() {
        cy.get('.nav').contains('Home').should('exist');
        cy.get('.nav').contains('About').should('exist');
        cy.get('.nav').contains('Contact').should('exist');
    })
    
    Cypress.Commands.add('logout', function () {
        cy.get('.btn.btn-success').click();
    })
    ```
    -->
    
### 10. Fixtures
1. Go to cypress/fixtures folder
2. Create new file `credentials.json`
3. Open file and paste
    ```json
    {
      "username": "user",
      "password": "password"
    }
    ```
4. In `commands.js` modify login function
    ```javascript
    Cypress.Commands.add('login', function() {
        cy.fixture('credentials').then((credentials) => {
            cy.get(selectorsLoginPage.username_input).type(credentials.username);
            cy.get(selectorsLoginPage.password_input).type(credentials.password);
            cy.get(selectorsLoginPage.sign_in_button).click();
        })
    })
    ```
5. Run tests to validate

### 11. Clean up
1. Stop docker containers `make stop`
2. List all containers `docker container ls -a`
3. Delete containers related to project `docker container rm (container id)`
4. List docker images `docker image ls` 
5. Remove docker images (use -f flag to force) `docker image rm (image id)`






