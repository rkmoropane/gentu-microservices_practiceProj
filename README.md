Final Project: Python Product Price Comparison Application

Project Overview

This project involves deploying multiple microservices to build an integrated Product Price Comparison Application using IBM Cloud Code Engine.

The application consists of:

- Product Details Microservice: A Python backend that provides API endpoints for retrieving product information.
- Dealer Pricing Microservice: A Node.js backend that provides API endpoints for retrieving dealer pricing information.
- Frontend Microservice: A web interface that allows users to select products, view dealers, and compare prices.

Estimated time: 1.5 hours

Learning Objectives

By completing this lab, you will be able to:

- Deploy Python backend microservices on IBM Cloud Code Engine.
- Deploy Node.js backend microservices on IBM Cloud Code Engine.
- Deploy a frontend microservice on IBM Cloud Code Engine.
- Configure frontend API endpoints using deployed microservice URLs.
- Test an integrated application and verify product pricing.

Prerequisites

Before starting, ensure that:

- You have access to the IBM Cloud lab environment.
- The Code Engine CLI is available and configured.
- The required IBM Container Registry namespace is set in "SN_ICR_NAMESPACE".
- You can access the Git repositories provided in the lab.

Important: Save all file changes and capture screenshots for Tasks 1–9. Screenshots are required for both AI-Graded and Peer-Graded submissions.

---

Part A: Deploying the Backend Microservices

Task 1: Deploy the Product Details Microservice

This microservice provides API endpoints for retrieving product information.

Deployment configuration:

Parameter| Value
Application name| "prodlist"
Build source| "https://github.com/ibm-developer-skills-network/dealer_evaluation_backend.git"
Build context directory| "products_list"
Port| "5000"
Container image| "us.icr.io/${SN_ICR_NAMESPACE}/prodlist"

Deployment command

Open the Code Engine CLI and execute:

ibmcloud ce application create \
  --name prodlist \
  --image us.icr.io/${SN_ICR_NAMESPACE}/prodlist \
  --registry-secret icr-secret \
  --port 5000 \
  --build-context-dir products_list \
  --build-source https://github.com/ibm-developer-skills-network/dealer_evaluation_backend.git

After deployment

1. Wait for the application deployment to complete successfully.
2. Copy the generated deployment URL.
3. Save the URL in a text editor. You will need it when configuring the frontend.
4. Capture a screenshot of the successful deployment.

Screenshot filename: "product_details_deploy.png"

Accepted alternative: "product_details_deploy.jpeg"

Troubleshooting: If you encounter "FAILED Wait failed for application 'prodlist'", try using the application name "prodlist1" and rerun the command.

Task 2: Deploy the Dealer Pricing Microservice

This microservice provides API endpoints for retrieving dealer pricing information.

Deployment configuration:

Parameter| Value
Application name| "dealerdetails"
Build source| "https://github.com/ibm-developer-skills-network/dealer_evaluation_backend.git"
Build context directory| "dealer_details"
Port| "8080"
Container image| "us.icr.io/${SN_ICR_NAMESPACE}/dealerdetails"

Deployment command

ibmcloud ce application create \
  --name dealerdetails \
  --image us.icr.io/${SN_ICR_NAMESPACE}/dealerdetails \
  --registry-secret icr-secret \
  --port 8080 \
  --build-context-dir dealer_details \
  --build-source https://github.com/ibm-developer-skills-network/dealer_evaluation_backend.git

After deployment

1. Wait for the application deployment to complete successfully.
2. Copy the generated deployment URL.
3. Save the URL in a text editor.
4. Capture a screenshot of the successful deployment.

Screenshot filename: "dealer_details_details.png"

Accepted alternative: "dealer_details_details.jpeg"

Troubleshooting: If you encounter "FAILED Wait failed for application 'dealerdetails'", try using the application name "dealerdetails1" and rerun the command.

---

Part B: Deploying the Frontend Microservice

Task 3: Clone the Frontend Repository

Open a new terminal and navigate to the project directory:

cd /home/project

Clone the frontend repository:

git clone https://github.com/ibm-developer-skills-network/dealer_evaluation_frontend.git

Navigate into the cloned repository:

cd dealer_evaluation_frontend

Verification

Check that the repository was cloned successfully:

ls

You should see the project files, including "index.html".

Screenshot filename: "git_clone.png"

Accepted alternative: "git_clone.jpeg"

Task 4: Update the API Deployment URLs

Open the "index.html" file in the "dealer_evaluation_frontend" directory.

Locate the API URL placeholders for the Product Details and Dealer Pricing microservices.

Replace the local development URLs with the actual deployment URLs obtained in Tasks 1 and 2.

The original local URLs are:

http://localhost:5000/
http://localhost:8080/

Replace them with the corresponding deployed URLs.

For example, if the Product Details microservice URL is "https://example-product-url/", use that URL in the product API configuration. Do the same for the Dealer Pricing microservice.

Important:

- Use your actual deployment URLs, not the example URL above.
- Keep each URL in the correct placeholder.
- Ensure each URL ends with a "/", as required by the lab instructions.
- Save the "index.html" file after making the changes.

Screenshot filename: "index_urlchanges.png"

Accepted alternative: "index_urlchanges.jpeg"

Task 5: Deploy the Frontend Microservice

Ensure that you are inside the frontend repository directory:

cd /home/project/dealer_evaluation_frontend

Deploy the frontend application to IBM Cloud Code Engine.

Deployment configuration:

Parameter| Value
Application name| "frontend"
Build source| "."
Port| "5001"
Container image| "us.icr.io/${SN_ICR_NAMESPACE}/frontend"

Deployment command

ibmcloud ce application create \
  --name frontend \
  --image us.icr.io/${SN_ICR_NAMESPACE}/frontend \
  --registry-secret icr-secret \
  --port 5001 \
  --build-source .

After deployment

1. Wait for the deployment to complete successfully.
2. Copy the frontend deployment URL.
3. Open the URL in your browser.
4. Capture a screenshot showing the successful deployment.

Screenshot filename: "frontend_deploy.png"

Accepted alternative: "frontend_deploy.jpeg"

Troubleshooting: If you encounter "FAILED Wait failed for application 'frontend'", try using the application name "frontend1" and rerun the command.

---

Part C: Testing the Application

Task 6: Verify the Homepage and Product List

1. Open the frontend deployment URL in your browser.
2. Allow time for the page to load, especially on the first visit.
3. Open the Products dropdown.
4. Verify that the product list has been populated.

Expected result: The homepage loads and the Products dropdown displays the available products.

Screenshot filename: "homepage.png"

Accepted alternative: "homepage.jpeg"

Task 7: Select a Product and View Its Dealers

1. On the homepage, select a product from the Products dropdown.
2. Wait for the application to retrieve the relevant dealer information.
3. Verify that the Dealers dropdown displays the dealers supplying the selected product.

Expected result: The application displays the dealers that supply the selected product.

Screenshot filename: "product_dealer.png"

Accepted alternative: "product_dealer.jpeg"

Task 8: Select a Dealer and View the Price

1. Select a product.
2. Wait for the Dealers dropdown to populate.
3. Select a specific dealer.
4. Allow approximately 10–20 seconds for the page to retrieve and display the pricing information.
5. Verify that the price charged by the selected dealer is displayed.

Expected result: The page shows the selected product, selected dealer, and corresponding price.

Screenshot filename: "product_dealer_price.png"

Accepted alternative: "product_dealer_price.jpeg"

Task 9: Compare Prices Across All Dealers

1. Select a product that is supplied by more than one dealer.
2. Choose the All Dealers option from the Dealers dropdown.
3. Wait for the pricing information to load.
4. Verify that the prices charged by all dealers supplying the selected product are displayed.

Expected result: The page shows the selected product, the All Dealers option, and the prices returned for all relevant dealers.

Screenshot filename: "product_all_dealers_prices.png"

Accepted alternative: "product_all_dealers_prices.jpeg"

---

Submission Checklist

Complete all nine tasks and verify that the required screenshots have been saved before submitting your project.

Task| Requirement| Screenshot| Done
1| Deploy Product Details microservice| "product_details_deploy.png"| [ ]
2| Deploy Dealer Pricing microservice| "dealer_details_details.png"| [ ]
3| Clone the frontend repository| "git_clone.png"| [ ]
4| Update the API URLs in "index.html"| "index_urlchanges.png"| [ ]
5| Deploy the frontend microservice| "frontend_deploy.png"| [ ]
6| Display the homepage with populated products| "homepage.png"| [ ]
7| Display the dealers supplying a selected product| "product_dealer.png"| [ ]
8| Display the price for a selected dealer| "product_dealer_price.png"| [ ]
9| Display prices for all dealers supplying a product| "product_all_dealers_prices.png"| [ ]

Submission Options

Choose one of the following evaluation methods:

1. Option 1: AI-Graded Submission and Evaluation
2. Option 2: Peer-Graded Submission and Evaluation

Both submission options require screenshots for Tasks 1–9.

Before submitting, ensure that:

- [ ] Both backend microservices have deployed successfully.
- [ ] The frontend repository has been cloned.
- [ ] The deployed API URLs have been saved in "index.html".
- [ ] The frontend has deployed successfully.
- [ ] All application tests have been completed.
- [ ] All nine screenshots have been captured and named correctly.
- [ ] All project file changes have been saved.

---

Project Summary

In this lab, a Product Price Comparison Application was deployed using IBM Cloud Code Engine. The integrated application consists of two backend microservices and a frontend microservice.

The completed application allows users to select products, identify dealers supplying those products, view individual dealer prices, and compare prices across all available dealers.

Author

Name: Ramahudu Kholofelo Moropane

Project: Python Product Price Comparison Application

Platform: IBM Cloud Code Engine

Course: Cognitive Class / IBM Skills Network
