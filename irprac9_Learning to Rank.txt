from sklearn import svm, model_selection
import numpy as np

# Data regarding a person watching 8 movies:
X = np.array ([[1, 1], [1, 2], [1, 3], [1, 1], [1, 1], [1, 1], [1, 3], [1, 2]])
# Person prefers: 1 > 2 > 3 (based on frequency)

# Relevance scores based on frequency of watching
y = np.array(["High relevance", "Low relevance","Irrelevant", "High relevance", "High relevance", "High relevance", "Irrelevant","Low relevance"])

# Define the RanksM model
model = svm.SVC(kernel='linear')

# Perform cross validation (model accuracy)
scores = model_selection.cross_val_score(model, X, y, cv=2)

# Train the model
model.fit (X, y)

# You can Update the new_ data movie to [1, 1], [1, 2] or [1, 3]
new_data = np.array ([[1, 3], [1, 1], [1, 2]])

# Person watches a 9th movie
predictions = model.predict(new_data)

print (f'Cross-validation scores: {scores}')
print (f'New data: {new_data}')
print(f'Predictions for new data: {predictions}')