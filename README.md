# Telco Customer Churn Retention Strategy (In Progress)

This academic project builds an end-to-end analytics framework for proactive customer retention in the telecommunications industry. The broader goal is to help a telecom provider identify customers at risk of churn, estimate revenue at risk, and eventually support intervention decisions through predictive and prescriptive analytics.

This repository currently focuses on the descriptive stage of the project. In Update 1, I cleaned and explored customer-level telecom data, engineered engagement-related features, performed customer segmentation with K-means clustering, applied PCA for dimensionality reduction and validation, and quantified revenue at risk across customer segments.

## Business Problem

Customer churn is costly for telecom providers, especially when high-value customers leave before the business can intervene. Rather than reacting only when customers are already cancelling, this project is designed to support a more proactive retention strategy by answering three questions:

1. Which customer groups are most at risk of churn
2. Which groups represent the greatest revenue exposure
3. How should retention resources eventually be prioritized

## Current Scope

This repository contains the first phase of the project: descriptive and diagnostic analytics.

Main tasks completed so far:
- Data cleaning and type correction
- Churn pattern exploration across contract type, internet type, and tenure
- Feature engineering, including service bundle count, tenure segment, and engagement index
- Customer segmentation using K-means clustering
- PCA-based validation of cluster structure
- Revenue-at-risk analysis by customer segment

## Key Findings

The descriptive analysis identified four customer segments with materially different churn and value profiles. The most urgent segment was the Early High-Risk group, which combined the highest churn rate with the largest weekly revenue at risk. A separate High-Value Bundled segment showed lower churn but higher customer lifetime value, making it strategically important to protect.

These findings suggest that retention strategy should not be based on churn probability alone. It should also account for customer value and segment-specific behavior.
