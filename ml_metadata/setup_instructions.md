# ML Metadata Setup Instructions for Visual Studio Code

This guide explains how to configure and use the ML metadata files in Visual Studio Code for machine learning projects.

## Overview

The `ml_metadata/` folder contains configuration files for managing machine learning project metadata and API integrations:

- **metadata.json**: Stores ML project metadata including model info, dataset details, training configuration, and performance metrics
- **api_config.json**: Configures integrations with ML platforms and APIs in VS Code

## Prerequisites

1. **Visual Studio Code** installed on your system
2. **Required VS Code Extensions**:
   - Python (ms-python.python)
   - Jupyter (ms-toolsai.jupyter)
   - Pylance (ms-python.vscode-pylance)
   - Optional: GitLens, Docker (for containerized ML workflows)

## Setting Up metadata.json

The `metadata.json` file tracks essential information about your ML project.

### Configuration Steps:

1. **Open the file** in VS Code:
   ```
   ml_metadata/metadata.json
   ```

2. **Fill in the project information**:
   - `project_name`: Name of your ML project
   - `version`: Current version of your project
   - `description`: Brief description of the project

3. **Configure model information**:
   - `model_name`: Name of your ML model
   - `model_type`: Type (e.g., "classification", "regression", "neural_network")
   - `framework`: ML framework used (e.g., "TensorFlow", "PyTorch", "scikit-learn")
   - `training_date`: Date when the model was trained

4. **Add dataset information**:
   - `dataset_name`: Name of your dataset
   - `data_source`: Source of the data
   - `features`: Array of feature names used in the model

5. **Document training configuration**:
   - `batch_size`: Training batch size
   - `epochs`: Number of training epochs
   - `learning_rate`: Learning rate value
   - `optimizer`: Optimizer used (e.g., "Adam", "SGD")

6. **Record performance metrics**:
   - Update metrics like `accuracy`, `precision`, `recall`, `f1_score`
   - Add custom metrics in the `custom_metrics` object

### Example:

```json
{
  "project_name": "Image Classification Model",
  "model_info": {
    "model_name": "ResNet50_Classifier",
    "model_type": "classification",
    "framework": "TensorFlow"
  },
  "training_config": {
    "batch_size": 32,
    "epochs": 50,
    "learning_rate": 0.001,
    "optimizer": "Adam"
  }
}
```

## Setting Up api_config.json

The `api_config.json` file manages API integrations for ML platforms.

### Configuration Steps:

1. **Open the file** in VS Code:
   ```
   ml_metadata/api_config.json
   ```

2. **Configure global API settings**:
   - Set `enabled: true` to activate API integrations
   - Adjust `timeout` and `retry_attempts` as needed
   - Set appropriate `log_level` ("DEBUG", "INFO", "WARNING", "ERROR")

3. **Enable ML platform integrations**:

   **For Hugging Face**:
   ```json
   "huggingface": {
     "enabled": true,
     "api_endpoint": "https://api-inference.huggingface.co",
     "api_key": "YOUR_HF_TOKEN",
     "model_id": "bert-base-uncased"
   }
   ```

   **For OpenAI**:
   ```json
   "openai": {
     "enabled": true,
     "api_endpoint": "https://api.openai.com/v1",
     "api_key": "YOUR_OPENAI_API_KEY",
     "model": "gpt-3.5-turbo"
   }
   ```

   **For Azure ML**:
   ```json
   "azure_ml": {
     "enabled": true,
     "subscription_id": "YOUR_SUBSCRIPTION_ID",
     "resource_group": "YOUR_RESOURCE_GROUP",
     "workspace_name": "YOUR_WORKSPACE",
     "api_key": "YOUR_API_KEY"
   }
   ```

4. **Configure VS Code extensions**:
   - Ensure Python, Jupyter, and Pylance settings are enabled
   - Set `type_checking` level for Pylance ("off", "basic", "strict")

5. **Set up data sources**:
   - Configure `local` paths for data and cache directories
   - For remote storage, enable and configure connection details

### Security Best Practices:

⚠️ **Important**: Never commit API keys directly to version control!

1. **Use environment variables**:
   - Store API keys in environment variables
   - Reference them in your code: `os.getenv("API_KEY")`

2. **Use VS Code settings**:
   - Store sensitive data in VS Code User Settings
   - File → Preferences → Settings → search for "Python: Env File"

3. **Create a `.env` file**:
   ```
   HUGGINGFACE_API_KEY=your_key_here
   OPENAI_API_KEY=your_key_here
   ```
   - Add `.env` to `.gitignore`

## Using the Configuration in VS Code

### Accessing Configuration Files:

1. **Quick Open**: Press `Ctrl+P` (Windows/Linux) or `Cmd+P` (Mac)
2. Type `metadata.json` or `api_config.json` to quickly open files

### VS Code Settings Integration:

1. Open **Settings** (`Ctrl+,` or `Cmd+,`)
2. Search for "Python: Data Science"
3. Configure Jupyter notebook settings
4. Set Python interpreter path for your ML environment

### Creating Tasks for ML Workflows:

Create a `.vscode/tasks.json` file to automate ML tasks:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Train Model",
      "type": "shell",
      "command": "python",
      "args": ["train.py"],
      "group": "build"
    },
    {
      "label": "Update Metadata",
      "type": "shell",
      "command": "python",
      "args": ["update_metadata.py"]
    }
  ]
}
```

### Setting Up Launch Configurations:

Create a `.vscode/launch.json` for debugging ML code:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: Train Model",
      "type": "python",
      "request": "launch",
      "program": "${workspaceFolder}/train.py",
      "console": "integratedTerminal",
      "env": {
        "PYTHONPATH": "${workspaceFolder}"
      }
    }
  ]
}
```

## Integration with Python Code

### Reading Metadata:

```python
import json

def load_metadata():
    with open('ml_metadata/metadata.json', 'r') as f:
        return json.load(f)

metadata = load_metadata()
print(f"Model: {metadata['model_info']['model_name']}")
```

### Reading API Configuration:

```python
import json
import os

def load_api_config():
    with open('ml_metadata/api_config.json', 'r') as f:
        config = json.load(f)
    
    # Replace with environment variables if available
    if 'HUGGINGFACE_API_KEY' in os.environ:
        config['ml_platforms']['huggingface']['api_key'] = \
            os.environ['HUGGINGFACE_API_KEY']
    
    return config

api_config = load_api_config()
```

### Updating Metadata After Training:

```python
import json
from datetime import datetime

def update_training_results(accuracy, loss):
    with open('ml_metadata/metadata.json', 'r') as f:
        metadata = json.load(f)
    
    metadata['performance_metrics']['accuracy'] = accuracy
    metadata['model_info']['last_updated'] = datetime.now().isoformat()
    
    with open('ml_metadata/metadata.json', 'w') as f:
        json.dump(metadata, f, indent=2)
```

## Recommended VS Code Extensions for ML

Install these extensions for enhanced ML development:

1. **Python** (ms-python.python) - Core Python support
2. **Jupyter** (ms-toolsai.jupyter) - Jupyter notebook support
3. **Pylance** (ms-python.vscode-pylance) - Fast Python language server
4. **autoDocstring** - Generate Python docstrings
5. **GitLens** - Enhanced Git integration
6. **Rainbow CSV** - CSV file visualization
7. **Data Wrangler** - Data exploration and cleaning
8. **Azure Machine Learning** - Azure ML integration (if using Azure)

## Troubleshooting

### Common Issues:

1. **API Key not working**:
   - Verify the key is correct and has proper permissions
   - Check if the key is properly loaded from environment variables
   - Ensure no extra whitespace in the key value

2. **JSON syntax errors**:
   - Use VS Code's built-in JSON validation
   - Look for missing commas, brackets, or quotes
   - Use a JSON formatter extension

3. **Python can't find the files**:
   - Verify the working directory in VS Code
   - Use absolute paths or proper relative paths
   - Check `PYTHONPATH` environment variable

4. **Extension not working**:
   - Reload VS Code window (`Ctrl+Shift+P` → "Reload Window")
   - Check extension is enabled and up to date
   - Review extension output logs

## Best Practices

1. **Version Control**:
   - Commit `metadata.json` to track model evolution
   - Add `api_config.json` to `.gitignore` (contains sensitive data)
   - Use a template `api_config.template.json` for team sharing

2. **Regular Updates**:
   - Update metadata after each training run
   - Document significant changes in `notes` field
   - Track model versions systematically

3. **Team Collaboration**:
   - Use consistent naming conventions
   - Document custom metrics clearly
   - Share configuration templates without sensitive data

4. **Automation**:
   - Create scripts to automatically update metadata
   - Integrate with CI/CD pipelines
   - Use VS Code tasks for common operations

## Additional Resources

- [VS Code Python Documentation](https://code.visualstudio.com/docs/python/python-tutorial)
- [VS Code Data Science Tutorial](https://code.visualstudio.com/docs/datascience/overview)
- [Jupyter Notebooks in VS Code](https://code.visualstudio.com/docs/datascience/jupyter-notebooks)
- [Python Environments in VS Code](https://code.visualstudio.com/docs/python/environments)

## Support

For issues or questions:
1. Check the troubleshooting section above
2. Review VS Code documentation
3. Consult your team's ML engineering guidelines
4. Open an issue in your project repository

---

**Last Updated**: 2025-12-25
**Version**: 1.0.0
