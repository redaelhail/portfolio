---
title: "Docker for code developement and deployement on a RaspberryPi"
date: 2025-3-07T09:26:41Z
draft: true
tags: [Docker]
cover:
  image: "demo_grid_view.png"
  alt: "<alt text>"
  caption: "<text>"
  relative: false # To use relative path for cover image, used in hugo Page-bundles
---

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>How to Set Up Apache Airflow on AWS EC2: A Beginner's Tutorial</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f9f9f9;
        }
        h1, h2, h3 {
            color: #2c3e50;
        }
        h1 {
            text-align: center;
            border-bottom: 2px solid #3498db;
            padding-bottom: 10px;
        }
        pre, code {
            background-color: #ecf0f1;
            padding: 10px;
            border-radius: 5px;
            overflow-x: auto;
        }
        pre {
            margin: 10px 0;
        }
        a {
            color: #3498db;
            text-decoration: none;
        }
        a:hover {
            text-decoration: underline;
        }
        .step {
            background-color: #fff;
            padding: 15px;
            margin: 15px 0;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .note {
            font-style: italic;
            color: #7f8c8d;
            margin-top: 5px;
        }
    </style>
</head>
<body>
    <h1>How to Set Up Apache Airflow on AWS EC2: A Beginner's Tutorial</h1>
    <p><strong>Published on April 17, 2025</strong></p>
    <p>If you're an ML practitioner looking to automate workflows, <strong>Apache Airflow</strong> is a powerful tool to orchestrate tasks like data preprocessing and model training. In this tutorial, we'll set up Airflow on an <strong>Amazon EC2</strong> instance, a virtual server in AWS. This guide is beginner-friendly for AWS users, with clear explanations of AWS components.</p>
    
    <h2>Why Use Airflow and AWS?</h2>
    <p>Airflow schedules and monitors workflows (e.g., ML pipelines) using <strong>DAGs</strong> (Directed Acyclic Graphs). Running Airflow on EC2 provides scalable compute resources, and AWS integrates seamlessly with storage like S3 for ML data. This setup is ideal for automating repetitive ML tasks.</p>

    <h2>Prerequisites</h2>
    <ul>
        <li><strong>AWS Account</strong>: Sign up at <a href="https://aws.amazon.com">aws.amazon.com</a>.</li>
        <li><strong>Python Knowledge</strong>: Familiarity with Python (e.g., for ML libraries).</li>
        <li><strong>Terminal Basics</strong>: For SSH and running commands.</li>
    </ul>

    <h2>Step-by-Step Setup</h2>

    <div class="step">
        <h3>Step 1: Launch an EC2 Instance</h3>
        <p><strong>What is EC2?</strong> EC2 is AWS's virtual machine service. We'll run Airflow here.</p>
        <ol>
            <li>Log in to the <a href="https://console.aws.amazon.com">AWS Management Console</a>.</li>
            <li>Navigate to <strong>EC2</strong> > <strong>Launch Instance</strong>.</li>
            <li>Name it <code>airflow-server</code>.</li>
            <li>Choose <strong>Amazon Linux 2 AMI</strong> (free tier eligible).</li>
            <li>Select <strong>t2.micro</strong> instance type (free tier).</li>
            <li>Create a new key pair (e.g., <code>airflow-key</code>), download <code>airflow-key.pem</code>, and store it securely.</li>
            <li>In <strong>Network Settings</strong>, allow <strong>SSH (port 22)</strong> and <strong>HTTP (port 80)</strong>.</li>
            <li>Launch the instance.</li>
        </ol>
        <p class="note"><strong>Note</strong>: The key pair is your access to the EC2 instance via SSH. Keep <code>airflow-key.pem</code> safe.</p>
    </div>

    <div class="step">
        <h3>Step 2: Create an IAM Role for EC2</h3>
        <p><strong>What is IAM?</strong> IAM (Identity and Access Management) controls permissions. Airflow on EC2 may need access to S3 for ML data.</p>
        <ol>
            <li>Go to <strong>IAM</strong> > <strong>Roles</strong> > <strong>Create Role</strong>.</li>
            <li>Select <strong>EC2</strong> as the trusted entity.</li>
            <li>Add <strong>AmazonS3FullAccess</strong> permission (use least privilege in production).</li>
            <li>Name it <code>EC2S3AccessRole</code> and create.</li>
            <li>In <strong>EC2</strong>, select your instance > <strong>Actions</strong> > <strong>Security</strong> > <strong>Modify IAM Role</strong> > Attach <code>EC2S3AccessRole</code>.</li>
        </ol>
        <p class="note"><strong>Note</strong>: This role lets EC2 access S3 without hardcoding credentials.</p>
    </div>

    <div class="step">
        <h3>Step 3: SSH into EC2 and Install Airflow</h3>
        <p>We'll connect to the EC2 instance and install Airflow.</p>
        <ol>
            <li>Open a terminal and navigate to where <code>airflow-key.pem</code> is stored.</li>
            <li>Secure the key file:
                <pre>chmod 400 airflow-key.pem</pre>
            </li>
            <li>SSH into the instance:
                <pre>ssh -i airflow-key.pem ec2-user@&lt;EC2-PUBLIC-IP&gt;</pre>
                Replace <code>&lt;EC2-PUBLIC-IP&gt;</code> with your instance’s public IP (found in EC2 console).
            </li>
            <li>Update the system:
                <pre>sudo yum update -y</pre>
            </li>
            <li>Install Python and pip:
                <pre>sudo yum install python3 python3-pip -y</pre>
            </li>
            <li>Install Airflow:
                <pre>pip3 install apache-airflow==2.7.3</pre>
            </li>
            <li>Set Airflow home and initialize the database:
                <pre>export AIRFLOW_HOME=~/airflow
airflow db init</pre>
            </li>
        </ol>
        <p class="note"><strong>Note</strong>: Airflow uses SQLite by default for simplicity. Use PostgreSQL in production.</p>
    </div>

    <div class="step">
        <h3>Step 4: Start Airflow Services</h3>
        <p>Airflow requires two processes: a webserver (for the UI) and a scheduler (for running tasks).</p>
        <ol>
            <li>Start the webserver:
                <pre>airflow webserver -p 8080 &</pre>
            </li>
            <li>Open a new terminal (or SSH session) and start the scheduler:
                <pre>ssh -i airflow-key.pem ec2-user@&lt;EC2-PUBLIC-IP&gt;
export AIRFLOW_HOME=~/airflow
airflow scheduler &</pre>
            </li>
        </ol>
        <p class="note"><strong>Note</strong>: The <code>&</code> runs processes in the background.</p>
    </div>

    <div class="step">
        <h3>Step 5: Access the Airflow UI</h3>
        <p>Access Airflow’s web interface to manage workflows.</p>
        <ol>
            <li>In the EC2 console, ensure your instance’s security group allows <strong>port 8080</strong>:
                <ul>
                    <li>Go to <strong>Security Groups</strong> > Select your instance’s group.</li>
                    <li>Add a rule: <strong>Custom TCP</strong>, port <code>8080</code>, source <code>0.0.0.0/0</code>.</li>
                </ul>
            </li>
            <li>Open a browser: <code>http://&lt;EC2-PUBLIC-IP&gt;:8080</code>.</li>
            <li>Log in with default credentials (username: <code>admin</code>, password: set during <code>airflow db init</code> or reset via CLI).</li>
        </ol>
        <p class="note"><strong>Warning</strong>: Opening port 8080 publicly is insecure for production. Use SSH tunneling or a VPN.</p>
    </div>

    <h2>Troubleshooting Tips</h2>
    <ul>
        <li><strong>UI Not Loading?</strong> Verify port 8080 is open in the security group.</li>
        <li><strong>S3 Access Issues?</strong> Ensure the IAM role is attached to the EC2 instance.</li>
        <li><strong>AWS Costs?</strong> Monitor usage in the AWS Billing Dashboard to stay in the free tier.</li>
    </ul>

    <h2>Next Steps</h2>
    <p>With Airflow running, you can create DAGs to automate ML workflows, like fetching data from S3, preprocessing, and training models. For production, consider <strong>Amazon Managed Workflows for Apache Airflow (MWAA)</strong>, a fully managed Airflow service.</p>
    <p>Want to dive deeper? Try writing a DAG to orchestrate an ML pipeline or integrate with other AWS services like SageMaker. Happy automating!</p>

</body>
</html>