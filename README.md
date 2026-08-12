# AI Customer Support Email Responder

An n8n workflow that reads incoming Gmail messages, figures out which ones are customer-support requests, and uses an AI agent to draft and send a reply.

The agent can also search a Pinecone knowledge base for relevant FAQs and policies before answering, so the response is based on the information provided to it rather than just the model's general knowledge.

![AI Customer Support Email Responder](screenshots/workflow.png)

---

## How it works

The workflow is basically:

New Gmail message
       ↓
Email classification
       ↓
Is it a support request?
    ↙             ↘
  Yes              No
   ↓                ↓
AI Support Agent   Stop
   ↓
Search knowledge base
   ↓
Generate reply
   ↓
Reply to the customer
Email trigger

The workflow starts with a Gmail trigger that checks for new messages. In the current setup, it checks for new emails every minute.

Email classification

Before sending an email to the AI agent, the workflow first checks what kind of message it is.

The classifier uses two categories:

Customer Support
Other

Support-related questions include things such as products, orders, shipping, returns, refunds, complaints, technical problems, policies, and FAQs.

Messages that don't need a support response are simply stopped here.

AI customer-support agent

If the message is classified as customer support, it is passed to the AI agent.

The agent receives the customer's email and prepares a response based on the available instructions and information from the knowledge base.

Knowledge base

The agent is connected to a Pinecone vector store containing FAQ and policy information.

When it needs information to answer a question, it can search the knowledge base and use the relevant results while preparing the response.

This is useful for questions where the answer needs to match a specific policy or piece of company information.

Sending the reply

Once the response has been generated, the workflow sends it back to the customer through Gmail as a reply to the original message.

Workflow components

This workflow uses:

n8n for the automation
Gmail for receiving and sending emails
OpenAI for the language model and embeddings
Pinecone for the vector knowledge base
AI Agent for generating the support response
Text Classifier for deciding which emails should be processed
Why the classifier is useful

There is no reason to send every email through the AI support agent.

For example, an unrelated email can be stopped before the AI agent is called:

New email
   ↓
Classify
   ↓
Support request?
   ├── Yes → AI Agent → Reply
   └── No  → Stop

This keeps the workflow focused on the messages it is actually designed to handle.

Why Pinecone is used

A support agent shouldn't have to rely entirely on the language model's built-in knowledge when answering questions about a specific business.

The Pinecone vector store gives the agent a separate knowledge base containing the information it should use, such as FAQs and policies.

This also makes the workflow easier to adapt: you can replace the knowledge base with your own information without changing the overall structure of the automation.

Requirements

To run the workflow, you'll need:

An n8n instance
A Gmail account connected to n8n
An OpenAI API connection
A Pinecone account and index
A knowledge base containing the FAQs, policies, or other information you want the agent to use
Importing the workflow
Download workflow.json from this repository.
Open your n8n instance.
Import the workflow JSON.
Connect your Gmail credentials.
Connect your OpenAI credentials.
Connect your Pinecone credentials.
Set your Pinecone index and namespace.
Add or connect your own FAQ and policy data to the knowledge base.
Send a test email and check the generated reply.

The workflow in this repository is a sanitized public copy, so you'll need to connect your own credentials after importing it.

Things you may want to change

Depending on how you want to use it, you can modify:

The email classification rules
The AI agent's instructions
The contents of the knowledge base
The Pinecone index and namespace
The Gmail account
The tone and style of the replies

The idea is to provide a working starting point that can be adapted to different customer-support use cases.

Example

Suppose a customer sends:

What is your return policy?

The workflow would:

Receive the email through Gmail.
Classify it as a customer-support request.
Pass it to the AI agent.
Search the knowledge base for the relevant return-policy information.
Use that information to prepare the response.
Reply to the customer through Gmail.

The same setup can be adapted for questions about shipping, refunds, orders, products, and other common support requests.

Repository structure
ai-customer-support-email-responder/
├── README.md
├── workflow.json
└── screenshots/
    └── workflow.png
A note before using it in production

This workflow is mainly intended as a learning and portfolio project.

If you use it for real customer emails, test it properly first. Make sure the information in the knowledge base is accurate and that the agent's instructions are appropriate for the type of support you want it to provide.

An AI-generated response can still be wrong, even when the workflow uses a knowledge base. The quality of the answers depends heavily on the information you provide and how the agent is instructed.

License

MIT
