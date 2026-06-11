# Python-Specific Copilot Instructions

## Overview
These instructions supplement [copilot-instructions-general.md](copilot-instructions-general.md) with Python-specific guidance. Refer to the general file for cross-project standards.

---

## Code Style & Formatting

### Ruff
- **Always run `ruff check` and `ruff format`** before finalizing changes
- Ruff provides both linting and formatting in one tool
- Use ruff configuration in `pyproject.toml` or `ruff.toml` for project consistency
- Code should pass `ruff check --fix && ruff format` without manual intervention

### Code Style
- Follow PEP 8 conventions (Ruff enforces this)
- Use lowercase_with_underscores for functions, variables, and module names
- Use CamelCase for class names
- Use SCREAMING_SNAKE_CASE for module-level constants
- Prefer descriptive names over brevity

### Type Hints
- **Public APIs**: Always include type hints
  - Function signatures must have parameter and return type hints
  - Example: `def process(data: np.ndarray) -> np.ndarray:`
- **Complicated Private Functions**: Add type hints for clarity
- **Data Science Functions**: Type hints encouraged, especially for array dimensions and dtypes
- **Notebooks**: Type hints optional (experimental/temporary development)
- Use `Optional[T]` for nullable types, avoid bare `None` as return type

### Imports
- Group imports: stdlib, third-party, local
- Use explicit imports; avoid `from module import *`
- Example:
  ```python
  import numpy as np
  import pandas as pd
  from sklearn.preprocessing import StandardScaler

  from my_module import helper_function
  ```

---

## Documentation & Comments

### Docstrings (NumPy Style)
All public functions and classes require docstrings in NumPy style:

```python
def calculate_mean(data: np.ndarray, axis: int = 0) -> np.ndarray:
    """
    Calculate the mean of an array along the specified axis.

    Parameters
    ----------
    data : np.ndarray
        Input array of numeric values.
    axis : int, optional
        Axis along which to compute the mean. Default is 0.

    Returns
    -------
    np.ndarray
        Mean values computed along the specified axis.

    Examples
    --------
    >>> data = np.array([[1, 2], [3, 4]])
    >>> calculate_mean(data)
    array([2., 3.])

    Notes
    -----
    This is a wrapper around np.mean for consistency with project conventions.
    """
    return np.mean(data, axis=axis)
```

### Docstring Sections
- **Short Summary**: One-line description
- **Extended Summary**: Optional longer explanation
- **Parameters**: Arguments with types and descriptions
- **Returns**: Return value(s) with types and descriptions
- **Raises**: Exceptions that may be raised
- **Examples**: Runnable code examples
- **Notes**: Additional context or warnings
- **References**: Links to papers, docs, or external resources

### Internal Comments
- Use moderate commenting: explain **why**, not **what**
- Code should be self-documenting; complex logic gets explanatory comments
- Example of good commenting:
  ```python
  # Normalize features before training to improve convergence
  X_scaled = scaler.fit_transform(X)
  ```

### Class Documentation
```python
class DataPreprocessor:
    """
    Preprocess raw data for machine learning models.

    This class handles common preprocessing tasks including normalization,
    outlier removal, and feature engineering.

    Attributes
    ----------
    scaler : sklearn.preprocessing.StandardScaler
        Fitted scaler for feature normalization.
    """
```

---

## Error Handling

### Exception Types
- Use built-in exceptions with helpful messages
- Raise specific exceptions (ValueError, TypeError, RuntimeError)
- Include context in error messages

### Error Patterns
```python
def load_model(filepath: str) -> dict:
    """Load a trained model from disk."""
    if not filepath.endswith('.pkl'):
        raise ValueError(
            f"Expected .pkl file, got: {filepath}. "
            "Use pickle format for model serialization."
        )

    if not os.path.exists(filepath):
        raise FileNotFoundError(f"Model not found: {filepath}")

    try:
        with open(filepath, 'rb') as f:
            return pickle.load(f)
    except pickle.UnpicklingError as e:
        raise RuntimeError(
            f"Failed to load model from {filepath}: {e}"
        ) from e
```

### Data Science Exceptions
- Return `None` for missing/invalid data is acceptable in data science contexts
- Document this in docstrings
- Example:
  ```python
  def get_feature_importance(model, feature_names: list) -> Optional[np.ndarray]:
      """
      Extract feature importance if model supports it.

      Returns None if the model doesn't have feature importance.
      """
      if not hasattr(model, 'feature_importances_'):
          return None
      return model.feature_importances_
  ```

---

## Testing

### Test Framework: pytest
- Use pytest as the standard testing framework
- Test discovery: `test_*.py` files or `*_test.py` files
- Run tests with: `pytest` or `pytest tests/`

### Test Organization
```
project/
├── src/
│   ├── preprocessing.py
│   ├── model.py
│   └── utils.py
├── tests/
│   ├── test_preprocessing.py
│   ├── test_model.py
│   ├── test_utils.py
│   └── fixtures.py          # Shared test fixtures
├── pyproject.toml
└── README.md
```

### Test Naming
- Test function names: `test_<what_is_being_tested>_<expected_outcome>`
  - `test_preprocessing_handles_missing_values`
  - `test_model_raises_on_invalid_input`
- Test file names: correspond to module being tested
  - `preprocessing.py` → `test_preprocessing.py`

### Test Structure (pytest)
```python
import pytest
import numpy as np
from my_module import preprocess_data

@pytest.fixture
def sample_data():
    """Fixture providing sample data for tests."""
    return np.array([[1, 2], [3, np.nan], [5, 6]])

def test_preprocessing_handles_missing_values(sample_data):
    """Test that preprocessing correctly handles NaN values."""
    result = preprocess_data(sample_data)
    assert not np.isnan(result).any()
    assert result.shape == sample_data.shape

def test_preprocessing_raises_on_invalid_type():
    """Test that preprocessing raises error for invalid input type."""
    with pytest.raises(TypeError):
        preprocess_data("not an array")
```

### Testing Data Science Code
- Use fixtures for common datasets
- Test mathematical correctness (shape, dtype, value ranges)
- Test edge cases (empty arrays, all NaN, extreme values)
- Mock external dependencies (API calls, file I/O)
- Example:
  ```python
  @pytest.fixture
  def model_and_data():
      X = np.random.randn(100, 10)
      y = np.random.randint(0, 2, 100)
      model = TrainableModel()
      return model, X, y

  def test_model_training_improves_accuracy(model_and_data):
      model, X, y = model_and_data
      initial_loss = model.evaluate(X, y)
      model.fit(X, y, epochs=5)
      final_loss = model.evaluate(X, y)
      assert final_loss < initial_loss
  ```

### When Not to Test
- Notebooks (experimental/temporary development)
- Third-party library functionality (trust their tests)
- Wrapper functions with no custom logic

---

## Data Science & Scientific Computing

### NumPy Conventions
- Use NumPy for numeric arrays (avoid Python lists for numerical operations)
- Prefer vectorized operations over loops
- Document array shapes in docstrings
- Example:
  ```python
  def normalize(X: np.ndarray) -> np.ndarray:
      """
      Normalize features to zero mean and unit variance.

      Parameters
      ----------
      X : np.ndarray, shape (n_samples, n_features)
          Input feature matrix.

      Returns
      -------
      np.ndarray, shape (n_samples, n_features)
          Normalized feature matrix.
      """
      mean = np.mean(X, axis=0)
      std = np.std(X, axis=0)
      return (X - mean) / (std + 1e-8)  # Add epsilon to avoid division by zero
  ```

### Pandas Conventions
- Use pandas DataFrames for tabular data
- Document column names and dtypes
- Chain operations readably
- Example:
  ```python
  def prepare_dataset(df: pd.DataFrame) -> pd.DataFrame:
      """
      Prepare dataset for modeling.

      Parameters
      ----------
      df : pd.DataFrame
          Raw data with columns: 'feature1', 'feature2', 'target'

      Returns
      -------
      pd.DataFrame
          Processed data with same structure, no missing values.
      """
      return (df
          .dropna()
          .assign(feature1=lambda x: (x['feature1'] - x['feature1'].mean()) / x['feature1'].std())
      )
  ```

### Model Saving/Loading
- Use built-in functionality when available
- Prefer pickle for scikit-learn models
- Use joblib for large models
- Save metadata alongside models
- Example:
  ```python
  import joblib

  def save_model(model, filepath: str) -> None:
      """Save trained model to disk."""
      joblib.dump(model, filepath)

  def load_model(filepath: str):
      """Load trained model from disk."""
      return joblib.load(filepath)
  ```

### Configuration & Hyperparameters
- Use dataclasses or ConfigDict for configuration
- Avoid magic numbers; use named constants
- Example:
  ```python
  from dataclasses import dataclass

  @dataclass
  class ModelConfig:
      """Configuration for model training."""
      learning_rate: float = 0.001
      epochs: int = 100
      batch_size: int = 32
      dropout: float = 0.5
  ```

---

## Dependencies

### Common Packages
- **Data Science**: numpy, pandas, scipy
- **ML/Deep Learning**: scikit-learn, tensorflow, pytorch
- **Visualization**: matplotlib, seaborn, plotly
- **I/O**: h5py (HDF5), zarr (array storage)
- **Testing**: pytest, pytest-cov
- **Type Checking**: mypy

### Environment Management
- Use virtual environments via conda
- Use `requirements.txt` for pinned dependencies or `pyproject.toml` for flexible versions
- Document Python version requirement
- Example `pyproject.toml`:
  ```toml
  [project]
  name = "my-ml-project"
  version = "0.1.0"
  requires-python = ">=3.9"
  dependencies = [
      "numpy>=1.20",
      "pandas>=1.3",
      "scikit-learn>=1.0",
  ]

  [tool.ruff]
  line-length = 100
  ```

### Before Adding Dependencies
- Check if stdlib or numpy/pandas provides the functionality
- Research maintenance and community adoption
- Ask before adding unfamiliar packages
- Prefer widely-used packages over experimental ones

---

## Common Patterns

### Array/DataFrame Operations
```python
# Vectorized (good)
result = np.sum(X * weights, axis=1)

# Loop (avoid)
result = np.array([np.sum(row * weights) for row in X])
```

### Function Composition
```python
def pipeline(X: np.ndarray) -> np.ndarray:
    """Apply preprocessing pipeline."""
    X = normalize(X)
    X = remove_outliers(X)
    X = engineer_features(X)
    return X
```

### Logging Progress (for long operations)
```python
import logging

logger = logging.getLogger(__name__)

def train_model(model, X, y, epochs: int) -> dict:
    """Train model with progress logging."""
    for epoch in range(epochs):
        loss = model.fit(X, y, verbose=0)
        if (epoch + 1) % 10 == 0:
            logger.info(f"Epoch {epoch + 1}/{epochs}, Loss: {loss:.4f}")
    return model
```

### Context Managers for Resource Management
```python
def process_large_file(filepath: str) -> np.ndarray:
    """Load and process large file."""
    with open(filepath, 'rb') as f:
        data = np.load(f)
    return preprocess(data)
```

---

## Notebooks vs. Scripts

### When to Use Notebooks (Temporary)
- Exploratory data analysis (EDA)
- Experimentation and prototyping
- Visualization and reporting
- Learning and teaching

### When to Use Scripts (Preferred)
- Production data pipelines
- Model training and evaluation
- Reusable utility functions
- CI/CD integration

### Best Practices
- Extract reusable code from notebooks into modules
- Notebooks should import functions from packages, not duplicate code
- Use notebooks for analysis, scripts for automation
- Version notebooks carefully (they don't diff well)

---

## Best Practices Checklist (Python)

- [ ] Code passes `ruff check` and `ruff format`
- [ ] Public APIs have NumPy-style docstrings with Examples
- [ ] Type hints on public APIs and complex private functions
- [ ] Tests exist for non-trivial functions (unit tests in tests/)
- [ ] Error messages are helpful and actionable
- [ ] NumPy/Pandas operations are vectorized, not looped
- [ ] Array shapes documented in docstrings
- [ ] No magic numbers; use named constants or config
- [ ] Models saved with joblib or pickle
- [ ] Dependencies are justified and well-established
- [ ] Changes follow established patterns and principles in the project
