# refx ml interview strategies

1. how do I turn my product problem into a prediction problem?
2. what's the data, what am I predicting?
3. what kind of problem is it?  classification, regression, etc…?
4. what are my success metrics?
   - measuring the performance of the model?
   - measuring the impact of the system?  asymmetric loss?
5. how are we evaluating the model?
   - validation criteria/set?
   - tracking production performance?
6. what are our features?  where are they coming from?  how are they represented?
7. what's our initial model?
   - simplest thing that could possibly work, given the problem type
   - how parametric do we want the model to be?  how interpretable do we want the model to be?
   - does the model choice dictate any feature transformations?
   - can the initial model beat the baseline?  do outputs predict inputs?  do we have enough data to learn the relation?
8. improve model
   - conisder other models that may perform better
   - does model under-perform?  more data?  more features?  not right model?
   - does model overfit? how do we mitigate?
9. deploy
   - how do we serve the model?
   - how do we retrain models? how do we version the model?
   - how do we split test models?