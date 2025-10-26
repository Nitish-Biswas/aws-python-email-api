# Serverless Python Email API on AWS

## 📜 Description

This project provides a simple, serverless REST API built using the Serverless Framework for AWS. It exposes an HTTP POST endpoint that accepts recipient email details (address, subject, body) and sends an email using Amazon Simple Email Service (SES).

The API runs on AWS Lambda and API Gateway, making it scalable and cost-effective (leveraging the AWS Free Tier for low usage). It includes basic error handling and supports local development using `serverless-offline`.

---

## ✨ Features

* **REST API Endpoint:** Single POST endpoint `/dev/send-email` to trigger email sending.
* **Email Sending:** Uses AWS SES for reliable email delivery.
* **Input Validation:** Checks for required fields (`receiver_email`, `subject`, `body_text`) and basic email format.
* **Error Handling:** Returns appropriate HTTP status codes (400, 403, 500, 503) for various error conditions (Bad Request, SES errors, etc.).
* **Local Development:** Supports offline testing using `serverless-offline`.
* **Configurable:** Sender email address is easily configured via `serverless.yml`.

---

## 📋 Prerequisites

Before you begin, ensure you have the following installed and configured:

1.  **AWS Account:** An active AWS account. You can utilize the [AWS Free Tier](https://aws.amazon.com/free/).
2.  **IAM User:** An AWS IAM User with programmatic access (Access Key ID and Secret Access Key) and necessary permissions (SES, Lambda, API Gateway, IAM for deployment).
3.  **Node.js & npm:** Node.js (v18 or later recommended) and npm. [Install Node.js](https://nodejs.org/).
4.  **Python & Pip:** Python 3.12 (as specified in `serverless.yml`) and pip. [Install Python](https://www.python.org/).
5.  **AWS CLI:** The AWS Command Line Interface. [Install AWS CLI](https://aws.amazon.com/cli/).
6.  **Serverless Framework CLI:** Install globally using npm:
    ```bash
    npm install -g serverless
    ```

---

## 🚀 Setup Instructions

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/Nitish-Biswas/aws-python-email-api.git
    cd aws-python-email-api
    ```

2.  **Configure AWS Credentials:** Configure your IAM user's credentials locally using the AWS CLI. This command will prompt you for your Access Key ID, Secret Access Key, default region, and output format. **Use `ap-south-1` as your default region** to match `serverless.yml`.
    ```bash
    aws configure
    ```

3.  **Verify SES Identities (Crucial for Sandbox):**
    * **Region:** Ensure you perform these steps in the `ap-south-1` region via the AWS Console or by specifying `--region ap-south-1` in the CLI commands.
    * **Sender:** Verify the email address you intend to send *from*. Replace the email below with your sender address:
        ```bash
        aws ses verify-email-identity --email-address sender_email@example.com
        ```
        Check your inbox for a verification email from AWS and click the link.
    * **Recipient (for Sandbox Testing):** By default, new AWS accounts are in the SES sandbox and can *only* send emails *to* verified addresses. For local testing, verify the recipient email address you plan to use. Replace the email below with your test recipient:
        ```bash
        aws ses verify-email-identity --email-address receiver_email@example.com
        ```
        Check that inbox for a verification email and click the link.
    * **Check Verification Status:**
        ```bash
        aws ses get-identity-verification-attributes --identities sender_email@example.com receiver_email@example.com
        ```
        Wait until `VerificationStatus` shows `Success` for both.
    * **(Optional) Request Production Access:** To send emails to *any* address (not just verified ones), you need to [request production access](https://docs.aws.amazon.com/ses/latest/dg/request-production-access.html) for SES in the `ap-south-1` region via the AWS Console.

4.  **Configure Sender Email:**
    * Open the `serverless.yml` file.
    * Locate the `environment` section and ensure `SENDER_EMAIL` is set to your **verified sender email address**:
        ```yaml
        environment:
          SENDER_EMAIL: nitishbiswas066@gmail.com # Make sure this is verified in SES ap-south-1
        ```

5.  **Create Python Virtual Environment:**
    ```bash
    python3 -m venv venv
    ```

6.  **Activate Virtual Environment:**
    * On macOS/Linux:
        ```bash
        source venv/bin/activate
        ```
    * On Windows:
        ```bash
        .\venv\Scripts\activate
        ```

7.  **Install Python Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
    *(Note: Ensure your `requirements.txt` file contains `boto3`)*

8.  **Install Node.js Dependencies:**
    ```bash
    npm install
    ```

---

## 💻 Usage (Local Testing)

1.  **Start the Local Server:** Run the following command from the project root directory:
    ```bash
    serverless offline start
    ```
    The API will be available locally, typically at `http://localhost:3000`.

2.  **Send a Test Request:** Open a new terminal (leave the server running) and use `curl` (or a tool like Postman/Insomnia) to send a POST request. **Remember to use a verified recipient email if your SES account is still in the sandbox.**

    ```bash
    curl -X POST http://localhost:3000/dev/send-email \
      -H "Content-Type: application/json" \
      -d '{
        "receiver_email": "receiver_email@example.com",
        "subject": "Test Email from Local Serverless API",
        "body_text": "This is a test email sent via the local serverless setup!"
      }'
    ```

3.  **Check Response:**
    * **Success:** You should receive a `200 OK` response with a JSON body like:
        ```json
        {
          "message": "Email sent successfully",
          "messageId": "...",
          "recipient": "receiver_email@example.com"
        }
        ```
        Check the recipient's inbox for the email.
    * **Error:** If something goes wrong (e.g., missing fields, invalid email, SES rejection), you'll get an appropriate error response (e.g., 400 Bad Request) with a JSON body explaining the error. Check the terminal where `serverless offline` is running for more detailed logs.

## Support
For issues and questions:
• Create an issue on GitHub

You can also contact the developer:
• **Name**: Nitish Biswas
• **Email**: nitishbiswas066@gmail.com
• **Linkedin**: [nitish-biswas1](https://www.linkedin.com/in/nitish-biswas1/)

---
