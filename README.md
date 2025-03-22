# GitLab Pipeline Bot

A conversational interface for searching and filtering GitLab pipelines using natural language queries.

## Overview

GitLab Pipeline Bot is an AI-powered tool that allows users to search for and filter GitLab pipelines using natural language. It uses OpenAI's GPT models to interpret user queries and translate them into GitLab API calls, making it easy to find specific pipelines without memorizing GitLab's API syntax or parameters.

## Features

- **Natural Language Interface**: Ask for pipeline information in plain English
- **Advanced Filtering**: Search pipelines by:
  - Task name
  - Status (created, pending, running, success, failed, canceled)
  - Environment (development, test, UAT, production)
  - Update date ranges
  - Username who triggered the pipeline
- **User-Friendly Output**: Well-formatted results that are easy to read and understand
- **Web Interface**: Simple Gradio chat interface accessible from any browser

## Prerequisites

- Python 3.7+
- OpenAI API key
- GitLab access token with appropriate permissions
- GitLab project ID

## Installation

1. Clone this repository:

   ```bash
   git clone https://github.com/yourusername/gitlab-pipeline-bot.git
   cd gitlab-pipeline-bot
   ```

2. Install required dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Create a `.env` file in the project root with the following variables:

   ```env
   OPENAI_API_KEY=your_openai_api_key
   GITLAB_ACCESS_TOKEN=your_gitlab_token
   GITLAB_BASE_URL=https://gitlab.com
   PROJECT_ID=your_gitlab_project_id
   ```

## Usage

1. Start the Gradio interface:

   ```bash
   python app.py
   ```

2. Open the provided URL in your browser (typically `http://127.0.0.1:7860`).

3. Start chatting with the bot using natural language queries such as:

   ```
   Show me failed pipelines from the last week
   Find all production deployments by username john.doe
   List successful pipelines for the login-service task
   ```

## Example Queries

```
Show me all failed pipelines for the authentication service
Find pipelines that were updated between January 1st and January 15th
List all production deployments that succeeded last month
Show me pipelines triggered by username smith in the UAT environment
```

## Technical Details

The application works in the following way:

1. User queries are sent to OpenAI's GPT-4o model
2. GPT interprets the natural language request and determines appropriate GitLab API parameters
3. The application makes authenticated calls to the GitLab API using the python-gitlab library
4. Results are formatted and returned to the user through the chat interface

### Code Example (Main Function)

```python
def fetch_gitlab_pipelines(task_name: str, status: str = None, 
                          environment: str = None, updated_after: str = None,
                          updated_before: str = None, username: str = None):
    try:
      project_id = os.getenv('PROJECT_ID')
      gitlab_access_token = os.getenv('GITLAB_ACCESS_TOKEN')
      gitlab_base_url = os.getenv('GITLAB_BASE_URL')

      # Configure GitLab client
      gl = gitlab.Gitlab(
          url=gitlab_base_url,
          private_token=gitlab_access_token
      )
      gl.auth()
      project = gl.projects.get(project_id)
    
      # Set up filters
      filters = {
          'per_page': 100,
          'get_all': True
      }

      # Add optional filters
      if status:
          filters['status'] = status
      if environment:
          filters['ref'] = environment
      if username:
          filters['username'] = username

      # Get pipelines with filters
      pipelines = project.pipelines.list(**filters)
      
      # Process results
      # ...
    except Exception as e:
        return f"Error searching pipelines: {e}"
```

## Required Dependencies

Add the following to your `requirements.txt` file:

```
openai>=1.0.0
python-dotenv>=1.0.0
gradio>=3.50.0
python-gitlab>=3.15.0
```

## Limitations

- Requires appropriate GitLab API permissions to view pipelines
- API rate limits apply for both GitLab and OpenAI
- The tool focuses only on pipeline information and does not handle other GitLab resources

## Troubleshooting

### Common Issues

1. **Authentication Errors**:

   ```
   Error searching pipelines: 401 Unauthorized
   ```

   Solution: Verify your GitLab access token has sufficient permissions.

2. **Environment Variables**:

   ```
   ValueError: OPENAI_API_KEY is not set
   ```

   Solution: Ensure your `.env` file contains all required variables.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
