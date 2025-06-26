# API Code Generation Prompt for Bolt Editor

## Core Prompt Template

```
You are an expert backend developer specializing in creating well-structured, production-ready APIs. Based on the following requirements, generate a complete API codebase with proper modular architecture.

**Project Requirements:**
- Application: [USER_APPLICATION_DESCRIPTION]
- Technology Stack: [NODE_JS_EXPRESS / USER_SPECIFIED_TECH]
- Database: [SPECIFY_IF_NEEDED]

**Architecture Requirements:**
Generate a **modular monolith** backend API following these architectural patterns. A modular monolith structures the application into independent modules based on logical boundaries, grouping related functionalities together to improve system cohesion.

1. **Modular Monolith Folder Structure:**
<pre> <code>
src/
├── modules/
│   ├── [moduleName]/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── middlewares/
│   │   └── index.js
│   └── [anotherModule]/
│       ├── controllers/
│       ├── models/
│       ├── repositories/
│       ├── routes/
│       ├── services/
│       ├── middlewares/
│       └── index.js
├── shared/
│   ├── middlewares/
│   ├── utils/
│   ├── config/
│   ├── database/
│   └── constants/
├── api/
│   └── index.js (route aggregator)
├── docs/
│   ├── postman/
│   │   ├── collections/
│   │   │   ├── [moduleName].postman_collection.json
│   │   │   └── [anotherModule].postman_collection.json
│   │   ├── environments/
│   │   │   ├── development.postman_environment.json
│   │   │   ├── staging.postman_environment.json
│   │   │   └── production.postman_environment.json
│   │   │ 
│   │   └── README.md
└── app.js
</code> </pre>

2. **For each identified domain/module, create a self-contained module with:**
   - **Model** (`modules/[moduleName]/models/[entityName]Model.js`): Data structure and validation schemas
   - **Repository** (`modules/[moduleName]/repositories/[entityName]Repository.js`): Database operations and queries
   - **Controller** (`modules/[moduleName]/controllers/[entityName]Controller.js`): Business logic and request handling
   - **Routes** (`modules/[moduleName]/routes/[entityName]Routes.js`): API endpoint definitions
   - **Services** (`modules/[moduleName]/services/[entityName]Service.js`): Business logic and inter-module communication
   - **Module Index** (`modules/[moduleName]/index.js`): Module exports and initialization
   - **Postman Collection** (`docs/postman/collections/[moduleName].postman_collection.json`): Complete API collection for the module

**Code Quality Standards:**
- **CRITICAL: The generated code must be error-free and ready to run directly without any issues**
- **All imports, exports, and dependencies must be correctly implemented and functional**
- **Every function, route, and module must be fully implemented - no placeholder code or TODO comments**
- **All syntax must be valid and executable JavaScript/Node.js code**
- Use camelCase for variables, functions, and file names
- Use PascalCase for constructor functions (if needed)
- Prefer functional programming patterns over classes
- Use ES6+ import/export syntax instead of CommonJS require
- Include comprehensive JSDoc documentation for all functions
- Add parameter validation and type checking
- Include proper error handling with try-catch blocks
- Use consistent HTTP status codes
- Follow RESTful API conventions
- Use modern ES6+ features (arrow functions, destructuring, async/await)
- **Ensure all module imports/exports work correctly without circular dependencies**
- **Verify all database operations are properly implemented**
- **Include complete, working middleware implementations**

**Required API Endpoints per Module:**
Generate standard CRUD operations for each entity within its respective module:
- GET /api/[module]/[entity] - Get all records (with pagination)
- GET /api/[module]/[entity]/:id - Get single record
- POST /api/[module]/[entity] - Create new record
- PUT /api/[module]/[entity]/:id - Update existing record
- DELETE /api/[module]/[entity]/:id - Delete record

**Documentation Requirements:**
For each endpoint, include:
- Purpose and functionality description
- Request parameters (path, query, body)
- Response format and status codes
- Example requests and responses
- Error handling scenarios
- **Postman Collection JSON** with complete request/response examples
- **Environment variables** setup for different deployment stages

**Postman Collection Requirements:**
For each module, generate a complete Postman collection that includes:

1. **Collection Structure:**
   - Collection name: "[ModuleName] API Collection"
   - Description with module overview and usage instructions
   - Variables for base URL, API version, and module-specific constants
   - Pre-request scripts for authentication tokens (if applicable)
   - Test scripts for response validation

2. **Request Organization:**
   - Folders organized by entity within the module
   - Requests named clearly (e.g., "Get All Users", "Create User", "Update User Profile")
   - Complete request configurations including headers, body, and query parameters
   - Example request bodies with sample data
   - Response examples for both success and error scenarios

3. **Environment Setup:**
   - Development environment with local server configuration
   - Staging environment with staging server details
   - Production environment with production server details
   - Common variables like API keys, tokens, and base URLs

4. **Collection Generation Script:**
   - Automated script to regenerate Postman collections when API changes
   - Script to validate collection completeness against route definitions
   - Integration with CI/CD pipeline for automatic collection updates

**Postman Collection Template Structure:**
```json
{
  "info": {
    "name": "[ModuleName] API Collection",
    "description": "Complete API collection for [ModuleName] module with all CRUD operations and examples",
    "version": "1.0.0",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "variable": [
    {
      "key": "baseUrl",
      "value": "{{base_url}}/api/[moduleName]",
      "type": "string"
    },
    {
      "key": "apiVersion",
      "value": "v1",
      "type": "string"
    }
  ],
  "event": [
    {
      "listen": "prerequest",
      "script": {
        "exec": [
          "// Pre-request scripts for authentication, logging, etc.",
          "console.log('Executing request to: ' + pm.request.url);"
        ]
      }
    },
    {
      "listen": "test",
      "script": {
        "exec": [
          "// Global test scripts",
          "pm.test('Response time is reasonable', function () {",
          "    pm.expect(pm.response.responseTime).to.be.below(2000);",
          "});",
          "",
          "pm.test('Response has success field', function () {",
          "    const response = pm.response.json();",
          "    pm.expect(response).to.have.property('success');",
          "});"
        ]
      }
    }
  ],
  "item": [
    {
      "name": "[EntityName] Operations",
      "item": [
        {
          "name": "Get All [EntityName]s",
          "request": {
            "method": "GET",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "url": {
              "raw": "{{baseUrl}}/[entity]?page=1&limit=10",
              "host": ["{{baseUrl}}"],
              "path": ["[entity]"],
              "query": [
                {
                  "key": "page",
                  "value": "1",
                  "description": "Page number for pagination"
                },
                {
                  "key": "limit",
                  "value": "10",
                  "description": "Number of records per page"
                }
              ]
            }
          },
          "response": [
            {
              "name": "Success Response",
              "originalRequest": {
                "method": "GET",
                "header": [],
                "url": {
                  "raw": "{{baseUrl}}/[entity]?page=1&limit=10",
                  "host": ["{{baseUrl}}"],
                  "path": ["[entity]"],
                  "query": [
                    {"key": "page", "value": "1"},
                    {"key": "limit", "value": "10"}
                  ]
                }
              },
              "status": "OK",
              "code": 200,
              "_postman_previewlanguage": "json",
              "header": [
                {
                  "key": "Content-Type",
                  "value": "application/json"
                }
              ],
              "body": "{\n  \"success\": true,\n  \"data\": [],\n  \"pagination\": {\n    \"page\": 1,\n    \"limit\": 10,\n    \"total\": 0,\n    \"pages\": 0\n  }\n}"
            }
          ]
        }
      ]
    }
  ]
}
```

**Environment Template Structure:**
```json
{
  "id": "development-environment",
  "name": "Development Environment",
  "values": [
    {
      "key": "base_url",
      "value": "http://localhost:3000",
      "enabled": true
    },
    {
      "key": "api_version",
      "value": "v1",
      "enabled": true
    },
    {
      "key": "auth_token",
      "value": "",
      "enabled": true
    }
  ]
}
```

**Additional Features to Include:**
- Module-specific input validation middleware
- Shared error handling middleware
- Request logging middleware
- CORS configuration
- Environment configuration
- Database connection setup
- Inter-module communication patterns
- Module initialization and bootstrapping
- **Automated Postman collection generation on API changes**
- **Collection validation and testing scripts**
- **Environment-specific collection variables**
- **API documentation synchronization with Postman collections**

**Code Structure Example for Each Module:**

**Module Index Template:**
```javascript
/**
 * [ModuleName] Module
 * Main entry point for the [moduleName] domain module
 */
import routes from './routes/index.js';
import * as services from './services/index.js';

const moduleInfo = {
  name: '[moduleName]',
  version: '1.0.0',
  routes,
  services
};

export default moduleInfo;
```

**Service Template:**
```javascript
/**
 * [EntityName] Service
 * Contains business logic and inter-module communication for [entity]
 */
import create[EntityName]Repository from '../repositories/[entityName]Repository.js';

/**
 * Creates a new [EntityName] service instance
 * @param {Object} dependencies - Service dependencies
 * @returns {Object} Service methods
 */
const create[EntityName]Service = (dependencies = {}) => {
  const [entityName]Repository = dependencies.[entityName]Repository || create[EntityName]Repository();

  /**
   * Business logic for creating [entity]
   * @param {Object} data - The [entity] data
   * @returns {Promise<Object>} Created [entity]
   */
  const create[EntityName] = async (data) => {
    // Business logic implementation
    return await [entityName]Repository.create(data);
  };

  /**
   * Business logic for retrieving [entity] by ID
   * @param {string} id - The [entity] ID
   * @returns {Promise<Object>} Retrieved [entity]
   */
  const get[EntityName]ById = async (id) => {
    // Business logic implementation
    return await [entityName]Repository.findById(id);
  };

  /**
   * Business logic for retrieving all [entity] records
   * @param {Object} filters - Query filters
   * @param {Object} pagination - Pagination options
   * @returns {Promise<Object>} Retrieved [entity] records with pagination
   */
  const getAll[EntityName]s = async (filters = {}, pagination = {}) => {
    // Business logic implementation
    const data = await [entityName]Repository.findAll(filters, pagination);
    return { data, pagination };
  };

  return {
    create[EntityName],
    get[EntityName]ById,
    getAll[EntityName]s
  };
};

export default create[EntityName]Service;
```

**Model Template:**
```javascript
/**
 * [EntityName] Model
 * Defines the data structure and validation for [entity] within [moduleName] module
 */

/**
 * Creates a new [EntityName] instance with validation
 * @param {Object} data - The [entity] data
 * @returns {Object} Validated [entity] object
 */
export const create[EntityName] = (data) => {
  // Validation logic
  const validated[EntityName] = {
    id: data.id,
    // Add other properties with validation
    createdAt: data.createdAt || new Date(),
    updatedAt: data.updatedAt || new Date()
  };

  return validated[EntityName];
};

/**
 * Validates [entity] data
 * @param {Object} data - Data to validate
 * @returns {Object} Validation result
 */
export const validate[EntityName] = (data) => {
  const errors = [];
  
  // Add validation rules
  if (!data.name) {
    errors.push('Name is required');
  }

  return {
    isValid: errors.length === 0,
    errors
  };
};

/**
 * [EntityName] schema definition
 */
export const [entityName]Schema = {
  id: { type: 'string', required: true },
  name: { type: 'string', required: true },
  createdAt: { type: 'date', default: () => new Date() },
  updatedAt: { type: 'date', default: () => new Date() }
};
```

**Repository Template:**
```javascript
/**
 * [EntityName] Repository
 * Handles all database operations for [entity] within [moduleName] module
 */

/**
 * Creates a new [EntityName] repository instance
 * @param {Object} database - Database connection
 * @returns {Object} Repository methods
 */
const create[EntityName]Repository = (database) => {
  /**
   * Retrieves all [entity] records
   * @param {Object} filters - Query filters
   * @param {Object} pagination - Pagination options
   * @returns {Promise<Array>} Array of [entity] records
   */
  const findAll = async (filters = {}, pagination = {}) => {
    try {
      // Implementation
      return [];
    } catch (error) {
      throw new Error(`Failed to retrieve [entity] records: ${error.message}`);
    }
  };

  /**
   * Retrieves a single [entity] by ID
   * @param {string} id - The [entity] ID
   * @returns {Promise<Object|null>} [Entity] record or null
   */
  const findById = async (id) => {
    try {
      // Implementation
      return null;
    } catch (error) {
      throw new Error(`Failed to retrieve [entity] by ID: ${error.message}`);
    }
  };

  /**
   * Creates a new [entity] record
   * @param {Object} data - [Entity] data
   * @returns {Promise<Object>} Created [entity] record
   */
  const create = async (data) => {
    try {
      // Implementation
      return data;
    } catch (error) {
      throw new Error(`Failed to create [entity]: ${error.message}`);
    }
  };

  /**
   * Updates an existing [entity] record
   * @param {string} id - [Entity] ID
   * @param {Object} data - Updated [entity] data
   * @returns {Promise<Object>} Updated [entity] record
   */
  const update = async (id, data) => {
    try {
      // Implementation
      return { ...data, id, updatedAt: new Date() };
    } catch (error) {
      throw new Error(`Failed to update [entity]: ${error.message}`);
    }
  };

  /**
   * Deletes a [entity] record
   * @param {string} id - [Entity] ID
   * @returns {Promise<boolean>} Deletion success status
   */
  const deleteById = async (id) => {
    try {
      // Implementation
      return true;
    } catch (error) {
      throw new Error(`Failed to delete [entity]: ${error.message}`);
    }
  };

  return {
    findAll,
    findById,
    create,
    update,
    deleteById
  };
};

export default create[EntityName]Repository;
```

**Controller Template:**
```javascript
/**
 * [EntityName] Controller
 * Handles HTTP requests and delegates to services for [entity] within [moduleName] module
 */
import create[EntityName]Service from '../services/[entityName]Service.js';

/**
 * Creates a new [EntityName] controller instance
 * @param {Object} dependencies - Controller dependencies
 * @returns {Object} Controller methods
 */
const create[EntityName]Controller = (dependencies = {}) => {
  const [entityName]Service = dependencies.[entityName]Service || create[EntityName]Service();

  /**
   * Get all [entity] records
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const getAll = async (req, res, next) => {
    try {
      const { page = 1, limit = 10, ...filters } = req.query;
      const pagination = { page: parseInt(page), limit: parseInt(limit) };
      
      const result = await [entityName]Service.getAll[EntityName]s(filters, pagination);
      
      res.status(200).json({
        success: true,
        data: result.data,
        pagination: result.pagination
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Get single [entity] by ID
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const getById = async (req, res, next) => {
    try {
      const { id } = req.params;
      const [entityName] = await [entityName]Service.get[EntityName]ById(id);
      
      if (![entityName]) {
        return res.status(404).json({
          success: false,
          message: '[EntityName] not found'
        });
      }
      
      res.status(200).json({
        success: true,
        data: [entityName]
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Create new [entity]
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const create = async (req, res, next) => {
    try {
      const [entityName]Data = req.body;
      const new[EntityName] = await [entityName]Service.create[EntityName]([entityName]Data);
      
      res.status(201).json({
        success: true,
        data: new[EntityName]
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Update existing [entity]
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const update = async (req, res, next) => {
    try {
      const { id } = req.params;
      const updateData = req.body;
      const updated[EntityName] = await [entityName]Service.update[EntityName](id, updateData);
      
      res.status(200).json({
        success: true,
        data: updated[EntityName]
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Delete [entity]
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const deleteEntity = async (req, res, next) => {
    try {
      const { id } = req.params;
      await [entityName]Service.delete[EntityName](id);
      
      res.status(200).json({
        success: true,
        message: '[EntityName] deleted successfully'
      });
    } catch (error) {
      next(error);
    }
  };

  return {
    getAll,
    getById,
    create,
    update,
    delete: deleteEntity
  };
};

export default create[EntityName]Controller;
```

**Routes Template:**
```javascript
/**
 * [EntityName] Routes
 * Defines API endpoints for [entity] within [moduleName] module
 */
import { Router } from 'express';
import create[EntityName]Controller from '../controllers/[entityName]Controller.js';

const router = Router();
const [entityName]Controller = create[EntityName]Controller();

/**
 * @route GET /api/[moduleName]/[entity]
 * @desc Get all [entity] records
 * @access Public/Private (based on requirements)
 */
router.get('/', [entityName]Controller.getAll);

/**
 * @route GET /api/[moduleName]/[entity]/:id
 * @desc Get single [entity] by ID
 * @access Public/Private (based on requirements)
 */
router.get('/:id', [entityName]Controller.getById);

/**
 * @route POST /api/[moduleName]/[entity]
 * @desc Create new [entity]
 * @access Private (usually)
 */
router.post('/', [entityName]Controller.create);

/**
 * @route PUT /api/[moduleName]/[entity]/:id
 * @desc Update existing [entity]
 * @access Private (usually)
 */
router.put('/:id', [entityName]Controller.update);

/**
 * @route DELETE /api/[moduleName]/[entity]/:id
 * @desc Delete [entity]
 * @access Private (usually)
 */
router.delete('/:id', [entityName]Controller.delete);

export default router;
```

**Generate the complete modular monolith codebase including:**
1. **PRODUCTION-READY CODE: All generated code must be immediately executable without errors, bugs, or missing implementations**
2. **COMPLETE IMPLEMENTATIONS: No placeholder code, TODOs, or incomplete functions - everything must be fully functional**
3. **WORKING DEPENDENCIES: All imports, exports, and module dependencies must be correctly implemented and tested**
4. Main application file (app.js) with ES6 imports and module initialization that runs without errors
5. Each domain module as a self-contained unit with full CRUD operations using ES6 modules - completely functional
6. Shared utilities, middlewares, and configuration in the shared folder with proper exports - all working implementations
7. Database configuration and connection in shared/database using ES6 imports - fully functional database setup
8. API route aggregator that combines all module routes with import statements - working route integration
9. Inter-module communication patterns and interfaces using ES6 modules - functional inter-module communication
10. Module bootstrapping and dependency injection with modern syntax - complete working system
11. Environment configuration setup using ES6 imports - functional environment management
12. Package.json with "type": "module" and all required dependencies - complete and accurate dependency list
13. Module-specific documentation and README files with ES6 import examples - comprehensive documentation
14. **Complete Postman collections for each module with full CRUD operations - immediately importable and functional**
15. **Environment-specific Postman environment files for development, staging, and production - ready to use**
16. **Collection validation and testing scripts for ensuring API-collection synchronization - functional validation**
17. **README documentation for Postman collection usage and import instructions - complete documentation**

**EXECUTION REQUIREMENTS:**
- **The generated application must start successfully with `npm start` or `node app.js`**
- **All API endpoints must be accessible and functional immediately after setup**
- **All database operations must work correctly with the specified database**
- **All middleware must function properly without errors**
- **All module imports/exports must resolve correctly**
- **The application must handle requests and responses properly**
- **Error handling must be implemented and functional**
- **All Postman collections must import successfully and work with the generated API**

**Specific Requirements:**
[ADD ANY ADDITIONAL SPECIFIC REQUIREMENTS HERE]

**CRITICAL SUCCESS CRITERIA:**
- **ZERO ERRORS**: The generated code must compile and run without any syntax errors, runtime errors, or missing dependencies
- **IMMEDIATE EXECUTION**: The application must start successfully on first run after basic setup (npm install)
- **COMPLETE FUNCTIONALITY**: All endpoints must work correctly and return proper responses
- **WORKING IMPORTS**: All ES6 module imports/exports must resolve correctly without circular dependencies
- **FUNCTIONAL DATABASE**: All database operations must be implemented and working (not just placeholder code)
- **READY-TO-USE**: No developer should need to fix, complete, or debug the generated code to make it functional
- **POSTMAN INTEGRATION**: All generated Postman collections must import and work immediately with the running API

Please ensure all code follows modern JavaScript/Node.js best practices, includes comprehensive error handling, and is production-ready with proper documentation. All Postman collections should be immediately importable and include complete request/response examples with proper environment variable setup.

**THE GENERATED CODEBASE MUST BE DEPLOYABLE AND FUNCTIONAL WITHOUT ANY MODIFICATIONS OR BUG FIXES.**
```

## How to Use This Prompt

### Step 1: Fill in the Variables
Replace the bracketed variables with your specific requirements:

- `[USER_APPLICATION_DESCRIPTION]`: Describe your application (e.g., "E-commerce platform with user management, product catalog, and order processing")
- `[NODE_JS_EXPRESS / USER_SPECIFIED_TECH]`: Specify technology stack
- `[SPECIFY_IF_NEEDED]`: Database choice (MongoDB, PostgreSQL, MySQL, etc.)

### Step 2: Add Specific Requirements
In the "Specific Requirements" section, add any additional needs such as:
- Authentication/Authorization requirements
- Third-party integrations
- Specific business logic
- Performance requirements
- Security considerations
- Postman collection customization requirements


### Example Usage

```
**Project Requirements:**
- Application: User Management System with authentication, profile management, and role-based access control
- Technology Stack: Node.js with Express.js framework
- Database: MongoDB with Mongoose ODM

**Domain Modules to Generate:**
- User Module (user authentication, profile, preferences)
- Auth Module (login, registration, password reset)
- Role Module (role management, permissions)

**Specific Requirements:**
- JWT-based authentication
- Role-based access control (Admin, Manager, User)
- Password encryption and validation
- Email verification system
- Audit logging for sensitive operations
- Input sanitization for security
- Module-to-module communication for user role verification
- Postman collections with authentication flow examples
- Environment variables for different API keys and database connections
```

## Expected Output Structure

When you use this prompt, you should receive:

1. **Modular monolith project structure** with domain-based modules
2. **Self-contained modules** with their own controllers, models, repositories, services, and routes
3. **Shared infrastructure** for common utilities, database, and configuration
4. **Inter-module communication patterns** for module-to-module interactions
5. **Comprehensive API documentation** for all endpoints organized by module
6. **Module initialization system** with proper dependency injection
7. **Shared middleware and utilities** for cross-cutting concerns
8. **Configuration management** for different environments
9. **Package.json** with all necessary dependencies
10. **Module-specific README files** with setup and usage instructions
11. **Complete Postman collections** for each module with importable JSON files
12. **Environment-specific Postman configurations** for development, staging, and production
13. **Collection validation tools** for ensuring API-documentation synchronization

## Tips for Best Results

1. **Define Domain Boundaries**: Clearly identify the logical domains/modules (e.g., User, Product, Order, Payment)
2. **Specify Module Dependencies**: Mention how modules need to communicate with each other
3. **Business Logic Separation**: Keep business logic in services, not controllers
4. **Shared vs Module-Specific**: Clarify what should be shared vs module-specific
5. **Module Interfaces**: Define how modules expose functionality to other modules
6. **Data Consistency**: Specify any cross-module data consistency requirements
7. **Postman Collection Organization**: Specify how you want collections organized (by module, by feature, etc.)
8. **Environment Variables**: Define what environment-specific variables need to be included
9. **Authentication Flow**: Specify how authentication should be handled in Postman collections
10. **Collection Maintenance**: Define when and how collections should be auto-generated or updated

This prompt will generate production-ready, well-documented API code that follows industry best practices and modular architecture principles, along with complete Postman collections that are immediately usable for API testing and documentation.




You are an expert backend developer specializing in creating well-structured, production-ready APIs. Based on the following requirements, generate a complete API codebase with proper modular architecture.

**Project Requirements:**
- Application:  Event Management System with booking options, admin to add events and all related operations
- Technology Stack: NODE_JS_EXPRESS
- Database: Mongo DB

**Architecture Requirements:**
Generate a **modular monolith** backend API following these architectural patterns. A modular monolith structures the application into independent modules based on logical boundaries, grouping related functionalities together to improve system cohesion.

1. **Modular Monolith Folder Structure:**
<code> <pre>
src/
├── modules/
│   ├── [moduleName]/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── repositories/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── middlewares/
│   │   └── index.js
│   └── [anotherModule]/
│       ├── controllers/
│       ├── models/
│       ├── repositories/
│       ├── routes/
│       ├── services/
│       ├── middlewares/
│       └── index.js
├── shared/
│   ├── middlewares/
│   ├── utils/
│   ├── config/
│   ├── database/
│   └── constants/
├── api/
│   └── index.js (route aggregator)
├── docs/
│   ├── postman/
│   │   ├── collections/
│   │   │   ├── [moduleName].postman_collection.json
│   │   │   └── [anotherModule].postman_collection.json
│   │   ├── environments/
│   │   │   ├── development.postman_environment.json
│   │   │   ├── staging.postman_environment.json
│   │   │   └── production.postman_environment.json
│   │   │ 
│   │   └── README.md
└── app.js
</code> </pre>

2. **For each identified domain/module, create a self-contained module with:**
   - **Model** (`modules/[moduleName]/models/[entityName]Model.js`): Data structure and validation schemas
   - **Repository** (`modules/[moduleName]/repositories/[entityName]Repository.js`): Database operations and queries
   - **Controller** (`modules/[moduleName]/controllers/[entityName]Controller.js`): Business logic and request handling
   - **Routes** (`modules/[moduleName]/routes/[entityName]Routes.js`): API endpoint definitions
   - **Services** (`modules/[moduleName]/services/[entityName]Service.js`): Business logic and inter-module communication
   - **Module Index** (`modules/[moduleName]/index.js`): Module exports and initialization
   - **Postman Collection** (`docs/postman/collections/[moduleName].postman_collection.json`): Complete API collection for the module

**Code Quality Standards:**
- **CRITICAL: The generated code must be error-free and ready to run directly without any issues**
- **All imports, exports, and dependencies must be correctly implemented and functional**
- **Every function, route, and module must be fully implemented - no placeholder code or TODO comments**
- **All syntax must be valid and executable JavaScript/Node.js code**
- Use camelCase for variables, functions, and file names
- Use PascalCase for constructor functions (if needed)
- Prefer functional programming patterns over classes
- Use ES6+ import/export syntax instead of CommonJS require
- Include comprehensive JSDoc documentation for all functions
- Add parameter validation and type checking
- Include proper error handling with try-catch blocks
- Use consistent HTTP status codes
- Follow RESTful API conventions
- Use modern ES6+ features (arrow functions, destructuring, async/await)
- **Ensure all module imports/exports work correctly without circular dependencies**
- **Verify all database operations are properly implemented**
- **Include complete, working middleware implementations**

**Required API Endpoints per Module:**
Generate standard CRUD operations for each entity within its respective module:
- GET /api/[module]/[entity] - Get all records (with pagination)
- GET /api/[module]/[entity]/:id - Get single record
- POST /api/[module]/[entity] - Create new record
- PUT /api/[module]/[entity]/:id - Update existing record
- DELETE /api/[module]/[entity]/:id - Delete record

**Documentation Requirements:**
For each endpoint, include:
- Purpose and functionality description
- Request parameters (path, query, body)
- Response format and status codes
- Example requests and responses
- Error handling scenarios
- **Postman Collection JSON** with complete request/response examples
- **Environment variables** setup for different deployment stages

**Postman Collection Requirements:**
For each module, generate a complete Postman collection that includes:

1. **Collection Structure:**
   - Collection name: "[ModuleName] API Collection"
   - Description with module overview and usage instructions
   - Variables for base URL, API version, and module-specific constants
   - Pre-request scripts for authentication tokens (if applicable)
   - Test scripts for response validation

2. **Request Organization:**
   - Folders organized by entity within the module
   - Requests named clearly (e.g., "Get All Users", "Create User", "Update User Profile")
   - Complete request configurations including headers, body, and query parameters
   - Example request bodies with sample data
   - Response examples for both success and error scenarios

3. **Environment Setup:**
   - Development environment with local server configuration
   - Staging environment with staging server details
   - Production environment with production server details
   - Common variables like API keys, tokens, and base URLs

4. **Collection Generation Script:**
   - Automated script to regenerate Postman collections when API changes
   - Script to validate collection completeness against route definitions
   - Integration with CI/CD pipeline for automatic collection updates

**Postman Collection Template Structure:**
```json
{
  "info": {
    "name": "[ModuleName] API Collection",
    "description": "Complete API collection for [ModuleName] module with all CRUD operations and examples",
    "version": "1.0.0",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "variable": [
    {
      "key": "baseUrl",
      "value": "{{base_url}}/api/[moduleName]",
      "type": "string"
    },
    {
      "key": "apiVersion",
      "value": "v1",
      "type": "string"
    }
  ],
  "event": [
    {
      "listen": "prerequest",
      "script": {
        "exec": [
          "// Pre-request scripts for authentication, logging, etc.",
          "console.log('Executing request to: ' + pm.request.url);"
        ]
      }
    },
    {
      "listen": "test",
      "script": {
        "exec": [
          "// Global test scripts",
          "pm.test('Response time is reasonable', function () {",
          "    pm.expect(pm.response.responseTime).to.be.below(2000);",
          "});",
          "",
          "pm.test('Response has success field', function () {",
          "    const response = pm.response.json();",
          "    pm.expect(response).to.have.property('success');",
          "});"
        ]
      }
    }
  ],
  "item": [
    {
      "name": "[EntityName] Operations",
      "item": [
        {
          "name": "Get All [EntityName]s",
          "request": {
            "method": "GET",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "url": {
              "raw": "{{baseUrl}}/[entity]?page=1&limit=10",
              "host": ["{{baseUrl}}"],
              "path": ["[entity]"],
              "query": [
                {
                  "key": "page",
                  "value": "1",
                  "description": "Page number for pagination"
                },
                {
                  "key": "limit",
                  "value": "10",
                  "description": "Number of records per page"
                }
              ]
            }
          },
          "response": [
            {
              "name": "Success Response",
              "originalRequest": {
                "method": "GET",
                "header": [],
                "url": {
                  "raw": "{{baseUrl}}/[entity]?page=1&limit=10",
                  "host": ["{{baseUrl}}"],
                  "path": ["[entity]"],
                  "query": [
                    {"key": "page", "value": "1"},
                    {"key": "limit", "value": "10"}
                  ]
                }
              },
              "status": "OK",
              "code": 200,
              "_postman_previewlanguage": "json",
              "header": [
                {
                  "key": "Content-Type",
                  "value": "application/json"
                }
              ],
              "body": "{\n  \"success\": true,\n  \"data\": [],\n  \"pagination\": {\n    \"page\": 1,\n    \"limit\": 10,\n    \"total\": 0,\n    \"pages\": 0\n  }\n}"
            }
          ]
        }
      ]
    }
  ]
}
```

**Environment Template Structure:**
```json
{
  "id": "development-environment",
  "name": "Development Environment",
  "values": [
    {
      "key": "base_url",
      "value": "http://localhost:3000",
      "enabled": true
    },
    {
      "key": "api_version",
      "value": "v1",
      "enabled": true
    },
    {
      "key": "auth_token",
      "value": "",
      "enabled": true
    }
  ]
}
```

**Additional Features to Include:**
- Module-specific input validation middleware
- Shared error handling middleware
- Request logging middleware
- CORS configuration
- Environment configuration
- Database connection setup
- Inter-module communication patterns
- Module initialization and bootstrapping
- **Automated Postman collection generation on API changes**
- **Collection validation and testing scripts**
- **Environment-specific collection variables**
- **API documentation synchronization with Postman collections**

**Code Structure Example for Each Module:**

**Module Index Template:**
```javascript
/**
 * [ModuleName] Module
 * Main entry point for the [moduleName] domain module
 */
import routes from './routes/index.js';
import * as services from './services/index.js';

const moduleInfo = {
  name: '[moduleName]',
  version: '1.0.0',
  routes,
  services
};

export default moduleInfo;
```

**Service Template:**
```javascript
/**
 * [EntityName] Service
 * Contains business logic and inter-module communication for [entity]
 */
import create[EntityName]Repository from '../repositories/[entityName]Repository.js';

/**
 * Creates a new [EntityName] service instance
 * @param {Object} dependencies - Service dependencies
 * @returns {Object} Service methods
 */
const create[EntityName]Service = (dependencies = {}) => {
  const [entityName]Repository = dependencies.[entityName]Repository || create[EntityName]Repository();

  /**
   * Business logic for creating [entity]
   * @param {Object} data - The [entity] data
   * @returns {Promise<Object>} Created [entity]
   */
  const create[EntityName] = async (data) => {
    // Business logic implementation
    return await [entityName]Repository.create(data);
  };

  /**
   * Business logic for retrieving [entity] by ID
   * @param {string} id - The [entity] ID
   * @returns {Promise<Object>} Retrieved [entity]
   */
  const get[EntityName]ById = async (id) => {
    // Business logic implementation
    return await [entityName]Repository.findById(id);
  };

  /**
   * Business logic for retrieving all [entity] records
   * @param {Object} filters - Query filters
   * @param {Object} pagination - Pagination options
   * @returns {Promise<Object>} Retrieved [entity] records with pagination
   */
  const getAll[EntityName]s = async (filters = {}, pagination = {}) => {
    // Business logic implementation
    const data = await [entityName]Repository.findAll(filters, pagination);
    return { data, pagination };
  };

  return {
    create[EntityName],
    get[EntityName]ById,
    getAll[EntityName]s
  };
};

export default create[EntityName]Service;
```

**Model Template:**
```javascript
/**
 * [EntityName] Model
 * Defines the data structure and validation for [entity] within [moduleName] module
 */

/**
 * Creates a new [EntityName] instance with validation
 * @param {Object} data - The [entity] data
 * @returns {Object} Validated [entity] object
 */
export const create[EntityName] = (data) => {
  // Validation logic
  const validated[EntityName] = {
    id: data.id,
    // Add other properties with validation
    createdAt: data.createdAt || new Date(),
    updatedAt: data.updatedAt || new Date()
  };

  return validated[EntityName];
};

/**
 * Validates [entity] data
 * @param {Object} data - Data to validate
 * @returns {Object} Validation result
 */
export const validate[EntityName] = (data) => {
  const errors = [];
  
  // Add validation rules
  if (!data.name) {
    errors.push('Name is required');
  }

  return {
    isValid: errors.length === 0,
    errors
  };
};

/**
 * [EntityName] schema definition
 */
export const [entityName]Schema = {
  id: { type: 'string', required: true },
  name: { type: 'string', required: true },
  createdAt: { type: 'date', default: () => new Date() },
  updatedAt: { type: 'date', default: () => new Date() }
};
```

**Repository Template:**
```javascript
/**
 * [EntityName] Repository
 * Handles all database operations for [entity] within [moduleName] module
 */

/**
 * Creates a new [EntityName] repository instance
 * @param {Object} database - Database connection
 * @returns {Object} Repository methods
 */
const create[EntityName]Repository = (database) => {
  /**
   * Retrieves all [entity] records
   * @param {Object} filters - Query filters
   * @param {Object} pagination - Pagination options
   * @returns {Promise<Array>} Array of [entity] records
   */
  const findAll = async (filters = {}, pagination = {}) => {
    try {
      // Implementation
      return [];
    } catch (error) {
      throw new Error(`Failed to retrieve [entity] records: ${error.message}`);
    }
  };

  /**
   * Retrieves a single [entity] by ID
   * @param {string} id - The [entity] ID
   * @returns {Promise<Object|null>} [Entity] record or null
   */
  const findById = async (id) => {
    try {
      // Implementation
      return null;
    } catch (error) {
      throw new Error(`Failed to retrieve [entity] by ID: ${error.message}`);
    }
  };

  /**
   * Creates a new [entity] record
   * @param {Object} data - [Entity] data
   * @returns {Promise<Object>} Created [entity] record
   */
  const create = async (data) => {
    try {
      // Implementation
      return data;
    } catch (error) {
      throw new Error(`Failed to create [entity]: ${error.message}`);
    }
  };

  /**
   * Updates an existing [entity] record
   * @param {string} id - [Entity] ID
   * @param {Object} data - Updated [entity] data
   * @returns {Promise<Object>} Updated [entity] record
   */
  const update = async (id, data) => {
    try {
      // Implementation
      return { ...data, id, updatedAt: new Date() };
    } catch (error) {
      throw new Error(`Failed to update [entity]: ${error.message}`);
    }
  };

  /**
   * Deletes a [entity] record
   * @param {string} id - [Entity] ID
   * @returns {Promise<boolean>} Deletion success status
   */
  const deleteById = async (id) => {
    try {
      // Implementation
      return true;
    } catch (error) {
      throw new Error(`Failed to delete [entity]: ${error.message}`);
    }
  };

  return {
    findAll,
    findById,
    create,
    update,
    deleteById
  };
};

export default create[EntityName]Repository;
```

**Controller Template:**
```javascript
/**
 * [EntityName] Controller
 * Handles HTTP requests and delegates to services for [entity] within [moduleName] module
 */
import create[EntityName]Service from '../services/[entityName]Service.js';

/**
 * Creates a new [EntityName] controller instance
 * @param {Object} dependencies - Controller dependencies
 * @returns {Object} Controller methods
 */
const create[EntityName]Controller = (dependencies = {}) => {
  const [entityName]Service = dependencies.[entityName]Service || create[EntityName]Service();

  /**
   * Get all [entity] records
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const getAll = async (req, res, next) => {
    try {
      const { page = 1, limit = 10, ...filters } = req.query;
      const pagination = { page: parseInt(page), limit: parseInt(limit) };
      
      const result = await [entityName]Service.getAll[EntityName]s(filters, pagination);
      
      res.status(200).json({
        success: true,
        data: result.data,
        pagination: result.pagination
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Get single [entity] by ID
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const getById = async (req, res, next) => {
    try {
      const { id } = req.params;
      const [entityName] = await [entityName]Service.get[EntityName]ById(id);
      
      if (![entityName]) {
        return res.status(404).json({
          success: false,
          message: '[EntityName] not found'
        });
      }
      
      res.status(200).json({
        success: true,
        data: [entityName]
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Create new [entity]
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const create = async (req, res, next) => {
    try {
      const [entityName]Data = req.body;
      const new[EntityName] = await [entityName]Service.create[EntityName]([entityName]Data);
      
      res.status(201).json({
        success: true,
        data: new[EntityName]
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Update existing [entity]
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const update = async (req, res, next) => {
    try {
      const { id } = req.params;
      const updateData = req.body;
      const updated[EntityName] = await [entityName]Service.update[EntityName](id, updateData);
      
      res.status(200).json({
        success: true,
        data: updated[EntityName]
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * Delete [entity]
   * @param {Object} req - Express request object
   * @param {Object} res - Express response object
   * @param {Function} next - Express next middleware function
   */
  const deleteEntity = async (req, res, next) => {
    try {
      const { id } = req.params;
      await [entityName]Service.delete[EntityName](id);
      
      res.status(200).json({
        success: true,
        message: '[EntityName] deleted successfully'
      });
    } catch (error) {
      next(error);
    }
  };

  return {
    getAll,
    getById,
    create,
    update,
    delete: deleteEntity
  };
};

export default create[EntityName]Controller;
```

**Routes Template:**
```javascript
/**
 * [EntityName] Routes
 * Defines API endpoints for [entity] within [moduleName] module
 */
import { Router } from 'express';
import create[EntityName]Controller from '../controllers/[entityName]Controller.js';

const router = Router();
const [entityName]Controller = create[EntityName]Controller();

/**
 * @route GET /api/[moduleName]/[entity]
 * @desc Get all [entity] records
 * @access Public/Private (based on requirements)
 */
router.get('/', [entityName]Controller.getAll);

/**
 * @route GET /api/[moduleName]/[entity]/:id
 * @desc Get single [entity] by ID
 * @access Public/Private (based on requirements)
 */
router.get('/:id', [entityName]Controller.getById);

/**
 * @route POST /api/[moduleName]/[entity]
 * @desc Create new [entity]
 * @access Private (usually)
 */
router.post('/', [entityName]Controller.create);

/**
 * @route PUT /api/[moduleName]/[entity]/:id
 * @desc Update existing [entity]
 * @access Private (usually)
 */
router.put('/:id', [entityName]Controller.update);

/**
 * @route DELETE /api/[moduleName]/[entity]/:id
 * @desc Delete [entity]
 * @access Private (usually)
 */
router.delete('/:id', [entityName]Controller.delete);

export default router;
```

**Generate the complete modular monolith codebase including:**
1. **PRODUCTION-READY CODE: All generated code must be immediately executable without errors, bugs, or missing implementations**
2. **COMPLETE IMPLEMENTATIONS: No placeholder code, TODOs, or incomplete functions - everything must be fully functional**
3. **WORKING DEPENDENCIES: All imports, exports, and module dependencies must be correctly implemented and tested**
4. Main application file (app.js) with ES6 imports and module initialization that runs without errors
5. Each domain module as a self-contained unit with full CRUD operations using ES6 modules - completely functional
6. Shared utilities, middlewares, and configuration in the shared folder with proper exports - all working implementations
7. Database configuration and connection in shared/database using ES6 imports - fully functional database setup
8. API route aggregator that combines all module routes with import statements - working route integration
9. Inter-module communication patterns and interfaces using ES6 modules - functional inter-module communication
10. Module bootstrapping and dependency injection with modern syntax - complete working system
11. Environment configuration setup using ES6 imports - functional environment management
12. Package.json with "type": "module" and all required dependencies - complete and accurate dependency list
13. Module-specific documentation and README files with ES6 import examples - comprehensive documentation
14. **Complete Postman collections for each module with full CRUD operations - immediately importable and functional**
15. **Environment-specific Postman environment files for development, staging, and production - ready to use**
16. **Collection validation and testing scripts for ensuring API-collection synchronization - functional validation**
17. **README documentation for Postman collection usage and import instructions - complete documentation**

**EXECUTION REQUIREMENTS:**
- **The generated application must start successfully with `npm start` or `node app.js`**
- **All API endpoints must be accessible and functional immediately after setup**
- **All database operations must work correctly with the specified database**
- **All middleware must function properly without errors**
- **All module imports/exports must resolve correctly**
- **The application must handle requests and responses properly**
- **Error handling must be implemented and functional**
- **All Postman collections must import successfully and work with the generated API**

**Specific Requirements:**
[ADD ANY ADDITIONAL SPECIFIC REQUIREMENTS HERE]

**CRITICAL SUCCESS CRITERIA:**
- **ZERO ERRORS**: The generated code must compile and run without any syntax errors, runtime errors, or missing dependencies
- **IMMEDIATE EXECUTION**: The application must start successfully on first run after basic setup (npm install)
- **COMPLETE FUNCTIONALITY**: All endpoints must work correctly and return proper responses
- **WORKING IMPORTS**: All ES6 module imports/exports must resolve correctly without circular dependencies
- **FUNCTIONAL DATABASE**: All database operations must be implemented and working (not just placeholder code)
- **READY-TO-USE**: No developer should need to fix, complete, or debug the generated code to make it functional
- **POSTMAN INTEGRATION**: All generated Postman collections must import and work immediately with the running API

Please ensure all code follows modern JavaScript/Node.js best practices, includes comprehensive error handling, and is production-ready with proper documentation. All Postman collections should be immediately importable and include complete request/response examples with proper environment variable setup.

**THE GENERATED CODEBASE MUST BE DEPLOYABLE AND FUNCTIONAL WITHOUT ANY MODIFICATIONS OR BUG FIXES.**
```