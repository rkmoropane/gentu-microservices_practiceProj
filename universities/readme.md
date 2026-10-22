# ☁️ Deploy, Update, and Scale Microservices with Serverless Framework in Python

![IBM Skills Network](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-CD0250EN-SkillsNetwork/images/IDSN-logo.png)

## 📌 Lab Overview

**Platform:** IBM Cloud Code Engine  
**Language:** Python  
**Framework:** Flask  
**Estimated Time:** 30 minutes

In this lab, you will build and deploy a UK university information application consisting of two independent microservices on IBM Cloud Code Engine.

You will learn how to deploy each microservice, scale its instances independently, and update its functionality without redeploying the entire application.

The application uses a JSON dataset of university information from the [University Domains List](https://raw.githubusercontent.com/Hipo/university-domains-list/master/world_universities_and_domains.json), filtered to include UK universities.

---

## 🎯 Learning Objectives

By completing this lab, you will be able to:

- Set up an IBM Cloud Code Engine project and CLI environment.
- Clone a GitHub repository containing two Python microservices.
- Deploy microservices as applications on Code Engine.
- Retrieve application details and access API endpoints.
- Scale a microservice by configuring its minimum number of instances.
- Update an existing microservice without stopping and redeploying the entire application.
- Modify a Flask API to support keyword-based searches.
- Verify API functionality using `curl`.

---

## 🧰 Prerequisites

Before starting, ensure that you have access to:

- The IBM Skills Network lab environment.
- IBM Cloud Code Engine.
- The preconfigured Code Engine CLI terminal.
- Git and the required project repository.
- Basic knowledge of Python, Flask, REST APIs, and command-line tools.

> **Note:** The lab environment provides the required IBM Cloud setup and registry namespace.

---

## 🚀 Part 1: Set Up the Code Engine Environment

1. Open the lab environment.
2. From the lab menu, select **Cloud → Code Engine**.
3. Click **Create Project**.
4. Wait for the Code Engine environment to finish preparing.
5. Once the environment is active, open **Code Engine CLI**.
6. Confirm that the terminal opens in the preconfigured working directory.

The lab environment automatically configures the Code Engine project and Kubernetes configuration.

---

## 📥 Part 2: Clone the Repository

Clone the lab repository:

```bash
git clone https://github.com/ibm-developer-skills-network/gentu-microservices_practiceProj.git
```

Navigate to the universities directory:

```bash
cd gentu-microservices_practiceProj/universities
```

List the directory contents:

```bash
ls
```

You should find two directories:

- `listing` — retrieves universities, including support for keyword searches.
- `websites` — retrieves university website URLs based on a college name.

Each directory contains the code and resources required to deploy its respective microservice.

---

## 🏗️ Part 3: Deploy the Listing Microservice

Create the `listing` application on Code Engine:

```bash
ibmcloud ce app create --name listing --image us.icr.io/${SN_ICR_NAMESPACE}/listing --registry-secret icr-secret --port 5000 --build-context-dir listing --build-source .
```

Wait for the deployment to complete.

Retrieve the application details and its URL:

```bash
ibmcloud ce app get -n listing
```

Copy the application URL and use it to test the API.

### Test the Listing Endpoint

Replace `<your-deployment-url>` with the URL returned by Code Engine:

```bash
curl <your-deployment-url>/colleges/Trinity
```

This endpoint retrieves universities whose names contain `Trinity`.

Check the application's instance information:

```bash
ibmcloud ce app get --name listing -q
```

Code Engine may initially start an instance only when a request arrives. Depending on the configuration, idle instances may later scale down.

---

## 🌐 Part 4: Deploy the Websites Microservice

Deploy the second microservice:

```bash
ibmcloud ce app create --name websites --image us.icr.io/${SN_ICR_NAMESPACE}/websites --registry-secret icr-secret --port 5000 --build-context-dir websites --build-source .
```

Wait for the deployment to complete and copy the application's URL.

### Test the Websites Endpoint

Test the endpoint using the exact university name:

```bash
curl <your-deployment-url>/websites/Trinity%20College%20of%20Music
```

The `%20` represents a space in a URL.

Now try searching with only a keyword:

```bash
curl <your-deployment-url>/websites/Trinity
```

**Expected result:** The original implementation does not support partial keyword searches, so this request will not return the expected list of websites.

This is the behaviour you will change in Part 6.

---

## 📈 Part 5: Scale the Listing Microservice

One advantage of microservices is that each service can be scaled independently according to its workload.

In this scenario, assume the listing API is receiving many requests and needs additional running instances.

Update the minimum number of instances:

```bash
ibmcloud ce app update --name listing --min 2
```

This configures Code Engine to maintain at least two instances, subject to the platform's resource availability and configuration.

Verify the application status:

```bash
ibmcloud ce app get --name listing -q
```

Wait for the required instances to start.

Test the listing endpoint again:

```bash
curl <your-deployment-url>/colleges/Trinity
```

With instances already running, requests may be faster because the service does not need to start an instance to handle the request.

> **Key concept:** Scale the microservice that needs more capacity rather than scaling the entire application unnecessarily.

---

## 🐍 Part 6: Update the Websites Microservice

The original websites microservice searches using an exact university name. You will modify it to support partial keyword matching.

### Step 1: Open the Python File

Open `app.py` inside the `websites` directory using the lab's IDE.

### Step 2: Replace the Implementation

Replace the existing code with the following implementation:

```python
from flask import Flask
from flask_cors import CORS
import json

app = Flask("University Websites")
CORS(app)

with open("UK_Universities.json", "r") as unifile:
    data = json.load(unifile)


@app.route("/websites/<name>")
def getCollegesWebsites(name):
    webpages = []

    for college in data:
        if college["name"].__contains__(name):
            for website in college["web_pages"]:
                webpages.append(website)

    return json.dumps({"Webpages": webpages}, indent=4)


if __name__ == "__main__":
    app.run(debug=True, port=5000)
```

### How the Updated Code Works

| Component | Purpose |
|---|---|
| `Flask` | Creates the Python web application. |
| `CORS` | Enables Cross-Origin Resource Sharing. |
| `json.load()` | Loads the university dataset from a JSON file. |
| `/websites/<name>` | Defines the API endpoint and accepts a search parameter. |
| `college["name"]` | Accesses a university's name. |
| `college["web_pages"]` | Retrieves the university's website URLs. |
| `json.dumps()` | Converts the response into formatted JSON. |

The updated endpoint searches university names for the supplied keyword and collects the website URLs for matching universities.

### Step 3: Update the Existing Application

Run the following command:

```bash
ibmcloud ce app update --name websites --image us.icr.io/${SN_ICR_NAMESPACE}/websites --registry-secret icr-secret --port 5000 --build-context-dir websites --build-source .
```

Wait for the application update to complete.

### Step 4: Verify the Changes

Test the updated endpoint:

```bash
curl <your-deployment-url>/websites/Trinity
```

**Expected result:** The API returns the website URLs for universities whose names contain `Trinity`, rather than requiring the complete university name.

The update changes the existing application without requiring you to redeploy the entire application or manually stop the microservice.

---

## 🧪 Part 7: Practice Exercises

Complete the following exercises to reinforce what you have learned.

### Exercise 1: Scale the Websites Microservice

Your task is to configure the websites microservice to maintain a minimum of two instances.

**Before scaling:**

Check the current instance status of both microservices:

```bash
ibmcloud ce app get --name listing -q
```

```bash
ibmcloud ce app get --name websites -q
```

If three or four instances are running across the services, wait approximately five minutes for idle instances to scale down, then check again.

This step helps conserve the lab environment's available memory.

**Scale the websites application:**

```bash
ibmcloud ce app update --name websites --min 2
```

Verify the result:

```bash
ibmcloud ce app get --name websites -q
```

Wait for the application to reach the configured minimum number of instances.

### Exercise 2: Implement Case-Insensitive University Search

Modify the `listing` microservice so that searches are case-insensitive.

For example, the search term `TriniTY` should return the same matching universities as `Trinity`.

**Requirements:**

1. Locate the Python file implementing the listing endpoint in the `listing` directory.
2. Find the code that compares university names with the supplied search parameter.
3. Update the comparison so that both the university name and search keyword are converted to the same case before matching.
4. Update the existing Code Engine application using the appropriate build and update command for the `listing` directory.
5. Verify the updated behaviour by calling the endpoint with mixed-case input.

For example:

```bash
curl <your-deployment-url>/colleges/TriniTY
```

**Expected result:** The endpoint returns the universities whose names contain `Trinity`, regardless of the capitalization of the search term.

> **Implementation hint:** Python's `.lower()` method can convert a string to lowercase before comparing it with another string.

---

## 🔍 Part 8: Important Commands Reference

| Task | Command |
|---|---|
| Clone repository | `git clone https://github.com/ibm-developer-skills-network/gentu-microservices_practiceProj.git` |
| Navigate to project | `cd gentu-microservices_practiceProj/universities` |
| List directories | `ls` |
| Get listing details | `ibmcloud ce app get --name listing -q` |
| Get websites details | `ibmcloud ce app get --name websites -q` |
| Scale listing | `ibmcloud ce app update --name listing --min 2` |
| Scale websites | `ibmcloud ce app update --name websites --min 2` |

---

## 🧠 Key Concepts Learned

### 1. Microservices

An application can be divided into smaller, independent services that perform different responsibilities.

In this lab:

- `listing` retrieves university information.
- `websites` retrieves website URLs for matching universities.

### 2. Independent Scaling

Each microservice can be scaled according to its own demand, reducing the need to allocate additional resources to services that do not need them.

### 3. Independent Updates

A microservice can be updated separately, allowing its functionality to change without requiring a full application redeployment.

### 4. API Testing

The `curl` command allows you to send HTTP requests to deployed endpoints and inspect their responses.

### 5. Keyword Search

Partial matching enables users to find multiple universities using a keyword instead of entering a complete university name.

### 6. Case-Insensitive Search

Converting strings to a common case before comparison allows searches to work regardless of capitalization.

---

## ✅ Completion Checklist

Use this checklist to track your progress.

- [ ] Created the Code Engine project.
- [ ] Cloned the lab repository.
- [ ] Deployed the `listing` microservice.
- [ ] Tested the listing endpoint.
- [ ] Deployed the `websites` microservice.
- [ ] Tested the original websites endpoint.
- [ ] Scaled the listing microservice to a minimum of two instances.
- [ ] Updated the websites API to support keyword searches.
- [ ] Verified keyword searches using `Trinity`.
- [ ] Scaled the websites microservice to a minimum of two instances.
- [ ] Updated the listing API to support case-insensitive searches.
- [ ] Verified the search using `TriniTY`.

---

## 🎉 Lab Summary

In this lab, you deployed a university information application composed of two Python microservices on IBM Cloud Code Engine.

You practised deploying services, testing their endpoints, scaling them independently, and updating application functionality without redeploying the entire application.

You also explored how keyword matching and case-insensitive searches can improve API usability.

**Main takeaway:** Microservices allow application components to be deployed, scaled, and updated independently, helping teams manage application workloads and evolve functionality more flexibly.

---

## 📚 Reference

- [IBM Skills Network](https://skills.network/)
- [Lab repository — gentu-microservices_practiceProj](https://github.com/ibm-developer-skills-network/gentu-microservices_practiceProj)
- [University Domains List dataset](https://raw.githubusercontent.com/Hipo/university-domains-list/master/world_universities_and_domains.json)
