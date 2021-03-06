## Description
PCA is dimension reduction of discrete feature, projects vectors to a low-dimensional space.
 PcaTrainBatchOp is train a model which can be used to batch predict and stream predict
 The calculation is done using eigen on the correlation or covariance matrix.

## Parameters
| Name | Description | Type | Required？ | Default Value |
| --- | --- | --- | --- | --- |
| k | the value of K. | Integer | ✓ |  |
| calculationType | compute type, be CORR, COV_SAMPLE, COVAR_POP. | String |  | "CORR" |
| selectedCols | Names of the columns used for processing | String[] |  | null |
| vectorCol | Name of a vector column | String |  | null |
| withMean | Centers the data with mean before scaling. | Boolean |  | true |
| withStd | Scales the data to unit standard deviation. true by default | Boolean |  | true |


## Script Example

#### Script

```python
data = np.array([
        [0.0,0.0,0.0],
        [0.1,0.2,0.1],
        [0.2,0.2,0.8],
        [9.0,9.5,9.7],
        [9.1,9.1,9.6],
        [9.2,9.3,9.9]
])

df = pd.DataFrame({"x1": data[:, 0], "x2": data[:, 1], "x3": data[:, 2]})

# batch source 
inOp = dataframeToOperator(df, schemaStr='x1 double, x2 double, x3 double', op_type='batch')

trainOp = PcaTrainBatchOp()\
       .setK(2)\
       .setSelectedCols(["x1","x2","x3"])

predictOp = PcaPredictBatchOp()\
        .setPredictionCol("pred")

# batch train
inOp.link(trainOp)

# batch predict
predictOp.linkFrom(trainOp,inOp)

predictOp.print()

# stream predict
inStreamOp = dataframeToOperator(df, schemaStr='x1 double, x2 double, x3 double', op_type='stream')

predictStreamOp = PcaPredictStreamOp(trainOp)\
        .setPredictionCol("pred")

predictStreamOp.linkFrom(inStreamOp)

predictStreamOp.print()

StreamOperator.execute()
```
#### Result

x1|x2|x3|pred
---|---|---|----
9.0|9.5|9.7|3.2280384305400736,1.1516225426477789E-4
0.2|0.2|0.8|0.13565076707329407,0.09003329494282108
9.2|9.3|9.9|3.250783163664603,0.0456526246528135
9.1|9.1|9.6|3.182618319978973,0.027469531992220464
0.1|0.2|0.1|0.045855205015063565,-0.012182917696915518
0.0|0.0|0.0|0.0,0.0




