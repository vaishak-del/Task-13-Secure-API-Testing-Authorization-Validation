# Task-13-Secure-API-Testing-Authorization-Validation
OWASP Juice Shop – Kali Linux Lab
Project Overview

This repository documents a hands-on API security assessment performed against a local instance of OWASP Juice Shop in a controlled laboratory environment.

The objective of this assessment was to evaluate authentication, authorization, and resource protection mechanisms by manually testing API endpoints and mapping findings to the OWASP API Security Top 10 (2023).

All testing was conducted in Kali Linux using command-line tools and a containerized deployment of the target application.

Laboratory Environment Setup

The target application was deployed using Docker:

sudo apt update && sudo apt install docker.io -y
sudo systemctl start docker
sudo docker pull bkimminich/juice-shop
sudo docker run -d -p 3000:3000 bkimminich/juice-shop


The application was accessed locally at:

http://localhost:3000

Tools Used

Kali Linux

cURL

Browser Developer Tools

Docker

Bash scripting

Testing Methodology

The assessment followed a structured manual testing approach:

API endpoint enumeration through browser network traffic analysis

Authentication testing using JWT tokens

Authorization validation through object identifier manipulation

Input validation testing using malformed JSON payloads

Rate limiting analysis using automated request loops

Identified Vulnerabilities
1. Broken Object Level Authorization (OWASP API1)

Endpoint Tested:
GET /api/Users/{id}

Exploitation Steps

Authenticated as a valid user.

Extracted the JWT token from the login response.

Accessed own profile using the token.

Modified the user ID in the request to access another user’s data.

Example:

curl http://localhost:3000/api/Users/3 \
-H "Authorization: Bearer <TOKEN>"

Observation

The server returned data belonging to other users without validating ownership of the requested resource.

Impact

Horizontal privilege escalation

Unauthorized access to user information

Exposure of administrative account details

OWASP Mapping

API1 – Broken Object Level Authorization (BOLA)

2. Unrestricted Resource Consumption (OWASP API4)

Endpoint Tested:
POST /rest/user/login

Rate Limiting Test
for i in {1..50}
do
curl -X POST http://localhost:3000/rest/user/login \
-H "Content-Type: application/json" \
-d '{"email":"admin@juice-sh.op","password":"wrong"}'
done

Observation

No 429 (Too Many Requests) response observed

No account lockout mechanism

No IP-based blocking detected

Impact

Brute-force attacks possible

Credential stuffing risk

Increased likelihood of account compromise

OWASP Mapping

API4 – Unrestricted Resource Consumption

OWASP API Top 10 Mapping Summary
Vulnerability	OWASP API Risk
IDOR	API1 – Broken Object Level Authorization
Missing Rate Limiting	API4 – Unrestricted Resource Consumption
Proof of Concept

Refer to the /screenshots directory for:

Successful JWT authentication

IDOR exploitation evidence

Brute-force test execution

Recommended Mitigations

Implement strict object-level authorization checks

Enforce rate limiting and account lockout policies

Apply strict JSON schema validation

Disable verbose error messages in production environments

Learning Outcomes

This project strengthened practical understanding of:

REST API attack surfaces

JWT-based authentication mechanisms

Authorization logic flaws

IDOR exploitation techniques

API abuse testing using command-line tools

Disclaimer

All testing was performed in a controlled local laboratory environment for educational and security research purposes only.
