pipeline {
    agent any // Or specify a docker agent like agent { docker 'python:3.9' }

    environment {
        # Load environment variables from .env file
        # This requires a Jenkins plugin or configuration to read .env,
        # or pass them as secrets/parameters.
        # For simplicity, assuming they are available or hardcoded for this example.
        # In a real scenario, use Jenkins credentials/secrets.
        DOCKER_BUILDKIT=1 // Enable BuildKit
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                git url: 'YOUR_GITHUB_REPO_URL', branch: 'main' // Replace with your repo URL and branch
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    # Make sure wait-for-it.sh is executable
                    sh 'chmod +x wait-for-it.sh'
                    sh "docker build -t my-django-app:${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // This is a placeholder. You need to write Django tests.
                    // This stage would typically run migrations and then run tests inside a container.
                    echo "Running tests (Placeholder)"
                    // Example (requires test configuration and data):
                    // sh "docker run --rm --network container:web_db_1 my-django-app:${env.BUILD_NUMBER} python manage.py test"
                    echo "Testing stage skipped as no tests are implemented yet."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Deploy using docker-compose
                    # Ensure Docker Compose is installed on the agent
                    # Use the built image tag
                    sh "docker-compose -f docker-compose.yml up -d --build web" # --build might be redundant if built in previous stage
                    echo "Application deployed via Docker Compose"
                    echo "Access the application at http://your-jenkins-agent-ip:8000"
                }
            }
        }
    }
}