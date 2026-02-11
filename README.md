# Home Inventory Web Client
A React.js + Bootstrap powered client for interacting with the Home Inventory Controller.

## Deploying

To configure manually, set environment variables in .env file. Use a URL for your API that is accessible from every device you want to use. Separately, run the backend API using PM2 and [PM2-installer](https://github.com/jessety/pm2-installer).

The following variables are currently supported:

- REACT_APP_API_URL:
  
example: `http://localhost:3001`
example: `http://my-pc-hostname:3001`

- REACT_APP_FOOTER_IMG_URL:

example: `http://my-image-link.jpg`

## Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

#### Available Scripts

In the project directory, you can run:

#### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

#### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

#### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

## API Definitions

<https://github.com/maddiethegm/Home-Inventory/blob/main/openapi.yaml>

```yaml
openapi: 3.0.0
info:
  title: Home Inventory API
  version: 1.0.0
  description: An API for managing user accounts and inventory in the Home Inventory application.
  
paths:
  /api/auth/register:
    post:
      summary: Register a new user
      tags: 
        - Authentication
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                Username:
                  type: string
                  maxLength: 50
                Password:
                  type: string
                Role:
                  type: string
                  maxLength: 255
                Email:
                  type: string
                  maxLength: 255
                DisplayName:
                  type: string
                  maxLength: 255
                AvatarURL:
                  type: string
                  maxLength: 255
                UITheme:
                  type: string
                  maxLength: 50
                Team:
                  type: string
                  maxLength: 255
                Bio:
                  type: string
                  maxLength: 255
                SQL_USER:
                  type: boolean
      responses:
        '201':
          description: User registered successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
        '400':
          description: Username, password, and role are required
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
        '409':
          description: User already exists
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
        '500':
          description: Registration failed
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
  /api/auth/login:
    post:
      summary: Authenticate a user and generate a JWT token
      tags: 
        - Authentication
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                Username:
                  type: string
                  maxLength: 50
                password:
                  type: string
      responses:
        '200':
          description: Login successful
          content:
            application/json:
              schema:
                type: object
                properties:
                  token:
                    type: string
        '400':
          description: Username and password are required
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
        '401':
          description: Invalid credentials
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
        '500':
          description: Authentication failed
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
  /api/users:
    get:
      summary: Fetch a list of all users
      tags:
        - Users
      responses:
        '200':
          description: A list of users
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
        '500':
          description: Database query failed
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    put:
      summary: Update user details by ID
      tags:
        - Users
      parameters:
        - in: path
          name: ID
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/User'
      responses:
        '200':
          description: User updated successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
        '400':
          description: User ID is required
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
        '500':
          description: Failed to update user details
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    delete:
      summary: Delete a user by ID
      tags:
        - Users
      parameters:
        - in: path
          name: ID
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: User deleted successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
        '500':
          description: Failed to delete user
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
  /api/users/{username}:
    get:
      summary: Fetch user details by username
      tags:
        - Users
      parameters:
        - in: path
          name: username
          required: true
          schema:
            type: string
            maxLength: 50
      responses:
        '200':
          description: User details retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: User not found
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
        '500':
          description: Failed to fetch user details
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
  /api/inventory:
    get:
      summary: Get inventory items based on query parameters.
      tags:
        - Inventory
      parameters:
        - in: query
          name: filterColumn
          schema:
            type: string
        - in: query
          name: searchValue
          schema:
            type: string
        - in: query
          name: exactMatch
          schema:
            type: boolean
      responses:
        '200':
          description: A list of inventory items.
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/InventoryItem'
        '500':
          description: Database query failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    put:
      summary: Update an inventory item.
      tags:
        - Inventory
      parameters:
        - in: path
          name: ID
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/InventoryItem'
      responses:
        '200':
          description: Inventory item updated successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  success:
                    type: boolean
        '500':
          description: Database update failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    delete:
      summary: Delete an inventory item.
      tags:
        - Inventory
      parameters:
        - in: path
          name: ID
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Inventory item deleted successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  success:
                    type: boolean
        '500':
          description: Database deletion failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    post:
      summary: Add a new inventory item.
      tags:
        - Inventory
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/InventoryItem'
      responses:
        '201':
          description: Inventory item added successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  success:
                    type: boolean
        '500':
          description: Database insertion failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
  /api/locations:
    get:
      summary: Get locations based on query parameters.
      tags:
        - Locations
      parameters:
        - in: query
          name: filterColumn
          schema:
            type: string
        - in: query
          name: searchValue
          schema:
            type: string
        - in: query
          name: exactMatch
          schema:
            type: boolean
      responses:
        '200':
          description: A list of locations.
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Location'
        '500':
          description: Database query failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    put:
      summary: Update a location.
      tags:
        - Locations
      parameters:
        - in: path
          name: ID
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Location'
      responses:
        '200':
          description: Location updated successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  success:
                    type: boolean
        '500':
          description: Database update failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    delete:
      summary: Delete a location.
      tags:
        - Locations
      parameters:
        - in: path
          name: ID
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Location deleted successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  success:
                    type: boolean
        '500':
          description: Database deletion failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
    post:
      summary: Add a new location.
      tags:
        - Locations
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Location'
      responses:
        '201':
          description: Location added successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  success:
                    type: boolean
        '500':
          description: Database insertion failed.
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: string
components:
  schemas:
    User:
      type: object
      properties:
        ID:
          type: string
          format: uuid
        Username:
          type: string
          maxLength: 50
        PasswordHash:
          type: string
        Role:
          type: string
          maxLength: 255
        Email:
          type: string
          maxLength: 255
        DisplayName:
          type: string
          maxLength: 255
        AvatarURL:
          type: string
          maxLength: 255
        UITheme:
          type: string
          maxLength: 50
        Team:
          type: string
          maxLength: 255
        Bio:
          type: string
          maxLength: 255
        SQL_USER:
          type: boolean
    InventoryItem:
      type: object
      properties:
        ID:
          type: string
          format: uuid
        Name:
          type: string
          maxLength: 255
        Description:
          type: string
          maxLength: 255
        Location:
          type: string
          maxLength: 255
        Bin:
          type: string
          maxLength: 255
        Quantity:
          type: integer
        Image:
          type: string
          maxLength: 255
        Owner:
          type: string
          maxLength: 255
    Location:
      type: object
      properties:
        ID:
          type: string
          format: uuid
        Name:
          type: string
          maxLength: 255
        Description:
          type: string
          maxLength: 255
        Building:
          type: string
          maxLength: 255
        Owner:
          type: string
          maxLength: 255
        Image:
          type: string
          maxLength: 255
securitySchemes:
  BearerAuth:
    type: http
    scheme: bearer
security:
  - BearerAuth: []
```
