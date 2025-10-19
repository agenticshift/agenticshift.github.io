+++
date = '2025-01-30T10:00:00+00:00'
draft = false
title = 'YAML Guide'
tags = ['YAML', 'Configuration', 'Data Serialization', 'DevOps', 'Tutorial']
+++

YAML (YAML is not a Markup Language) is a human-readable data serialization standard that can be used in conjunction with all programming languages and is often used for configuration files and data exchange between languages with different data structures.
A comprehensive guide to YAML (YAML Ain't Markup Language) - a human-readable data serialization language commonly used for configuration files, data exchange, and infrastructure as code.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Basic Syntax](#basic-syntax)
3. [Data Types](#data-types)
4. [Collections](#collections)
5. [Advanced Features](#advanced-features)
6. [Best Practices](#best-practices)
7. [Common Use Cases](#common-use-cases)
8. [Troubleshooting](#troubleshooting)

---

## Introduction

### Key Characteristics

- **Human-readable**: Easy to read and write
- **Minimal syntax**: Uses indentation and simple symbols
- **Language-agnostic**: Works with any programming language
- **Superset of JSON**: Any valid JSON is also valid YAML
- **Data-focused**: Designed for data representation, not documents

### File Extension

YAML files typically use `.yaml` or `.yml` extensions.

---

## Basic Syntax

### Comments

```yaml
# This is a single-line comment

# Comments must start with the hash symbol (#)
# They can appear anywhere in the file

key: value  # Inline comments are also supported
```

### Key-Value Pairs

```yaml
# Basic key-value syntax
name: John Doe
age: 30
city: New York

# Values with spaces (no quotes needed)
title: Senior Software Engineer

# Empty values
middle_name:
nickname: null
```

### Indentation Rules

```yaml
# YAML uses spaces for indentation (NOT tabs)
# Standard is 2 or 4 spaces per level
# Be consistent throughout the file

parent:
  child1: value1
  child2: value2
  nested:
    grandchild: value3
```

**Important**: Never mix tabs and spaces. Use spaces only!

### Multi-line Strings

```yaml
# Literal block scalar (preserves newlines)
description: |
  This is a multi-line string.
  Each line break is preserved.
  Perfect for text blocks.

# Folded block scalar (converts newlines to spaces)
summary: >
  This is also a multi-line string,
  but line breaks will be converted
  to spaces when parsed.

# Long single-line string
long_text: "This is a very long string that continues on one line without breaking"
```

---

## Data Types

### Strings

```yaml
# Unquoted strings
name: John Doe
company: Tech Corp

# Single-quoted strings (literal)
message: 'Hello, World!'
path: 'C:\Users\John'

# Double-quoted strings (can use escape sequences)
greeting: "Hello, \"World\"!"
newline: "Line 1\nLine 2"

# Multi-word strings
description: This is a string with multiple words

# Strings with special characters
url: https://example.com
email: user@example.com
```

### Numbers

```yaml
# Integers
age: 25
count: -10
hex: 0x1A
octal: 0o14

# Floats
price: 19.99
rating: 4.5
scientific: 1.23e+10

# Special numeric values
infinity: .inf
negative_infinity: -.inf
not_a_number: .nan
```

### Booleans

```yaml
# True values
is_active: true
is_enabled: True
is_valid: TRUE
is_correct: yes
is_available: Yes
is_present: YES
is_on: on
is_ready: On

# False values
is_deleted: false
is_disabled: False
is_invalid: FALSE
is_wrong: no
is_unavailable: No
is_absent: NO
is_off: off
is_stopped: Off
```

### Null Values

```yaml
# Various ways to represent null
nothing: null
empty: Null
tilde: ~
blank:
```

### Dates and Timestamps

```yaml
# ISO 8601 dates
date: 2025-09-30
datetime: 2025-09-30T10:30:00Z
datetime_with_tz: 2025-09-30T10:30:00+05:30

# Alternative formats
canonical: 2025-09-30T10:30:00.000Z
iso8601: 2025-09-30t10:30:00.00-05:00
```

---

## Collections

### Lists (Arrays)

```yaml
# Block style (preferred)
fruits:
  - apple
  - banana
  - orange
  - mango

# Flow style (inline)
colors: [red, green, blue, yellow]

# Mixed content
items:
  - string_item
  - 123
  - true
  - null

# Nested lists
matrix:
  - [1, 2, 3]
  - [4, 5, 6]
  - [7, 8, 9]

# List of objects
users:
  - name: John
    age: 30
  - name: Jane
    age: 25
  - name: Bob
    age: 35
```

### Dictionaries (Maps/Objects)

```yaml
# Block style
person:
  name: John Doe
  age: 30
  email: john@example.com
  address:
    street: 123 Main St
    city: New York
    zip: 10001

# Flow style
coordinates: {x: 10, y: 20, z: 30}

# Nested dictionaries
company:
  name: Tech Corp
  departments:
    engineering:
      employees: 50
      manager: Alice
    sales:
      employees: 30
      manager: Bob
```

### Complex Nested Structures

```yaml
# Combining lists and dictionaries
database:
  host: localhost
  port: 5432
  credentials:
    username: admin
    password: secret
  tables:
    - users
    - products
    - orders

# List of dictionaries with nested structures
employees:
  - name: John Doe
    role: Developer
    skills:
      - Python
      - JavaScript
      - Docker
    projects:
      - name: Project A
        status: active
      - name: Project B
        status: completed
  - name: Jane Smith
    role: Designer
    skills:
      - Figma
      - Photoshop
    projects:
      - name: Project C
        status: active
```

---

## Advanced Features

### Anchors and Aliases (Reusing Data)

```yaml
# Define an anchor with &
defaults: &default_settings
  timeout: 30
  retries: 3
  debug: false

# Reference with *
development:
  <<: *default_settings
  debug: true

production:
  <<: *default_settings
  timeout: 60

# Multiple anchors
database: &db_config
  host: localhost
  port: 5432

cache: &cache_config
  host: localhost
  port: 6379

services:
  primary_db:
    <<: *db_config
    name: main_db
  redis:
    <<: *cache_config
    name: cache_server
```

### Merge Keys

```yaml
# Base configurations
base: &base
  name: Base
  version: 1.0

extended: &extended
  <<: *base
  feature: enabled

# Final merge
final:
  <<: [*base, *extended]
  custom: value
```

### Document Separators

```yaml
# Multiple YAML documents in one file
---
document: first
content: This is document 1
---
document: second
content: This is document 2
---
document: third
content: This is document 3
```

### Explicit Typing

```yaml
# Force types with !!
string_number: !!str 123
integer_string: !!int "456"
float_value: !!float 123
binary_data: !!binary |
  R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7

# Explicitly typed collections
set_values: !!set
  ? item1
  ? item2
  ? item3

ordered_map: !!omap
  - first: 1
  - second: 2
  - third: 3
```

### Multi-line Keys

```yaml
# Keys can span multiple lines
? |
  This is a very long key
  that spans multiple lines
: value for the long key

# More practical example
? !!str "line1\nline2"
: "multi-line key with value"
```

### Special Characters and Escaping

```yaml
# Strings with special characters
special: "String with: colon, {braces}, [brackets]"
escaped: "Quote: \" Backslash: \\ Newline: \n Tab: \t"

# Preserving formatting
code: |
  def hello():
      print("Hello, World!")
      return True

# Keys with special characters
"key:with:colons": value
"key with spaces": value
```

---

## Best Practices

### 1. Indentation and Formatting

```yaml
# GOOD: Consistent 2-space indentation
person:
  name: John
  address:
    street: Main St
    city: NYC

# AVOID: Inconsistent or tab indentation
person:
    name: John
  address:
      street: Main St
```

### 2. Quoting Strings

```yaml
# Quote when necessary
version: "1.0"  # Without quotes: numeric
port: "8080"    # Without quotes: numeric
flag: "yes"     # Without quotes: boolean

# No quotes needed for simple strings
name: John Doe
title: Senior Engineer

# Quote strings with special characters
message: "Value with: colons"
path: "C:\\Users\\Documents"
```

### 3. Comments for Documentation

```yaml
# Server configuration
server:
  host: localhost  # Development environment
  port: 8080       # Default HTTP port
  
  # SSL configuration (production only)
  ssl:
    enabled: true
    certificate: /path/to/cert.pem
```

### 4. Use Anchors to Avoid Repetition

```yaml
# GOOD: Using anchors
common: &common_config
  timeout: 30
  retries: 3

service_a:
  <<: *common_config
  name: ServiceA

service_b:
  <<: *common_config
  name: ServiceB

# AVOID: Repetition
service_a:
  timeout: 30
  retries: 3
  name: ServiceA

service_b:
  timeout: 30
  retries: 3
  name: ServiceB
```

### 5. Organize Complex Files

```yaml
# Group related configurations
---
# Application settings
application:
  name: MyApp
  version: 2.0.0

# Database configuration
database:
  host: localhost
  port: 5432

# Logging configuration
logging:
  level: INFO
  format: json
```

### 6. Validate YAML Files

Always validate your YAML files using tools like:
- Online validators (yamllint.com)
- Command-line tools (yamllint)
- IDE plugins

---

## Common Use Cases

### 1. Docker Compose

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
      - NGINX_PORT=80
    depends_on:
      - database

  database:
    image: postgres:13
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: myapp
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

### 2. Kubernetes Configuration

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

### 3. CI/CD Pipeline (GitHub Actions)

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '16'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run tests
      run: npm test
    
    - name: Build
      run: npm run build
    
    - name: Deploy
      if: github.ref == 'refs/heads/main'
      run: npm run deploy
      env:
        DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
```

### 4. Application Configuration

```yaml
# config.yaml
app:
  name: MyApplication
  version: 1.0.0
  environment: production

server:
  host: 0.0.0.0
  port: 8080
  ssl:
    enabled: true
    cert_path: /etc/ssl/certs/cert.pem
    key_path: /etc/ssl/private/key.pem

database:
  primary:
    host: db.example.com
    port: 5432
    name: myapp_db
    username: dbuser
    password: ${DB_PASSWORD}  # Environment variable
    pool_size: 10
    timeout: 30
  
  replica:
    host: db-replica.example.com
    port: 5432

cache:
  type: redis
  host: cache.example.com
  port: 6379
  ttl: 3600

logging:
  level: INFO
  format: json
  outputs:
    - type: file
      path: /var/log/app/app.log
    - type: console

features:
  feature_a: enabled
  feature_b: disabled
  feature_c:
    enabled: true
    config:
      param1: value1
      param2: value2
```

### 5. Ansible Playbook

```yaml
---
- name: Deploy Web Application
  hosts: webservers
  become: yes
  
  vars:
    app_name: myapp
    app_version: 1.0.0
    deploy_path: /var/www/{{ app_name }}
  
  tasks:
    - name: Install required packages
      apt:
        name:
          - nginx
          - python3
          - python3-pip
        state: present
        update_cache: yes
    
    - name: Create application directory
      file:
        path: "{{ deploy_path }}"
        state: directory
        owner: www-data
        group: www-data
        mode: '0755'
    
    - name: Copy application files
      copy:
        src: ./app/
        dest: "{{ deploy_path }}"
        owner: www-data
        group: www-data
    
    - name: Start nginx service
      service:
        name: nginx
        state: started
        enabled: yes
```

---

## Troubleshooting

### Common Errors and Solutions

#### 1. Indentation Errors

```yaml
# WRONG: Inconsistent indentation
person:
  name: John
   age: 30  # Extra space causes error

# CORRECT:
person:
  name: John
  age: 30
```

#### 2. Tab Characters

```yaml
# WRONG: Using tabs (not visible here but common issue)
# Always use spaces, never tabs

# Check your editor settings to convert tabs to spaces
```

#### 3. Unquoted Special Values

```yaml
# WRONG: Special YAML values interpreted incorrectly
version: 1.0  # Parsed as float
status: yes   # Parsed as boolean

# CORRECT: Quote when you want strings
version: "1.0"
status: "yes"
```

#### 4. Missing Spaces After Colons

```yaml
# WRONG: No space after colon
name:John

# CORRECT: Space required after colon
name: John
```

#### 5. Incorrect List Syntax

```yaml
# WRONG: Missing space after dash
items:
  -apple
  -banana

# CORRECT: Space required after dash
items:
  - apple
  - banana
```

#### 6. Multiline String Issues

```yaml
# WRONG: Indentation not aligned
description: |
This is wrong
  This too

# CORRECT: Consistent indentation
description: |
  This is correct
  This too
```

### Validation Tools

```bash
# Using yamllint (Python)
pip install yamllint
yamllint config.yaml

# Using online validators
# - https://www.yamllint.com/
# - https://jsonformatter.org/yaml-validator

# Using IDE plugins
# - VSCode: YAML extension by Red Hat
# - IntelliJ: Built-in YAML support
# - Vim: vim-yaml plugin
```

---

## Quick Reference Card

```yaml
# Basic syntax
key: value
number: 123
boolean: true
null_value: null

# Strings
string: Plain text
quoted: "With quotes"
multiline: |
  Preserve
  newlines

# Lists
list:
  - item1
  - item2
inline: [a, b, c]

# Dictionaries
dict:
  key1: value1
  nested:
    key2: value2
inline_dict: {a: 1, b: 2}

# Anchors & Aliases
default: &anchor
  value: 123
reference:
  <<: *anchor

# Comments
# Single line comment
key: value  # Inline comment

# Multiple documents
---
doc1: first
---
doc2: second
```

---

## Additional Resources

### Official Documentation
- [YAML Official Specification](https://yaml.org/spec/)
- [YAML Reference Card](https://yaml.org/refcard.html)

### Tools and Libraries
- **Python**: PyYAML, ruamel.yaml
- **JavaScript**: js-yaml, yaml
- **Go**: gopkg.in/yaml.v3
- **Ruby**: Psych (built-in)
- **Java**: SnakeYAML

### Learning Resources
- YAML Tutorial: https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started
- Interactive YAML Parser: https://yaml-online-parser.appspot.com/
- YAML Lint: https://www.yamllint.com/
- YAML Tools: https://yamline.com/

---

## Conclusion

YAML is a powerful and flexible data serialization format that balances human readability with machine parseability. By following the syntax rules and best practices outlined in this guide, you can effectively use YAML for configuration files, data exchange, and infrastructure as code.

Key takeaways:
- Always use spaces (never tabs) for indentation
- Be consistent with your formatting
- Quote strings when necessary
- Use anchors to avoid repetition
- Validate your YAML files regularly
- Keep it simple and readable

Happy YAML coding! 🎉
