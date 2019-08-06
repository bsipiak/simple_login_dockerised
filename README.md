## 1. Getting started with Cypress

###1.1 Get a sandbox environment
	
- Clone the repository: `git clone https://github.com/bsipiak/simple_login_dockerised.git`

- Build and start environment: `make up`

- Our project will be available under: http://localhost:8080

### 1.2 Install cypress

- Install cypress in our project: `yarn add cypress --dev`
- Start cypress: `sudo yarn run cypress open`

### 1.3 First test!

- Create file "my_first_test.js" in folder cypress/integration
- Paste there this code: 	
```javascript
    describe('Go to main page', function () {
        it('should check if important elements are present on main page', function () {
            
            cy.visit('http://localhost:8080');
        
            cy.get('#inputUsername');
            cy.get('#inputPassword');
            cy.get(':checkbox[value="remember-me"]');
            cy.get('.btn').contains('Sign in');
        });
    });
```

### 1.4 Exercise

Write test that will: 
1. login to our platform,
2. check if all elements in menu are present,
3. check if status says "logged in",
3. logout,
4. check if we are back on page 'http://localhost:8080/login.php'

#####Data
- Login: user
- Password: password

#####Hints
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






