<!-- SPDX-License-Identifier: Apache-2.0 -->

---
name: Rule processor template
about: A template to guide the specification of a new rule processor
title: New rule 999 - Description
labels: 'Component: Rules'
assignees: ''

---

The steps that the `handleTransaction` function of the rule processor needs to follow to do all the work required by this rule are detailed below.

## Summary of exit and error conditions

*Any ERROR produced out of the rule processor **must** be dumped in subRuleRef `.err`, with an explicit reason that describes the error. The ruleResult.result must **always** be false.*

See https://frmscoe.atlassian.net/wiki/spaces/FRMS/pages/53608449/Standard+Rule+Processor+Exit+and+Error+Conditions

### Rule-specific exit/error conditions:

1. `.x00` - If the current transaction is unsuccessful
2. `.x01` - If the number of returned results is less than the minimum number of results required for a deterministic outcome
3. `.x02` - If the set of results returned by the rule query is empty and cannot be categorised
4. `.x03` - If statistical modelling of history is inconclusive, but the recent trend is upwards
5. `.x04` - If statistical modelling of history is inconclusive, but the recent trend is downwards

## Rule processor execution steps

### 1. Set up and handle the rule config

Locate all the statements related to the rule config at the top of the file.

### 2. Early exit conditions

### 3. Query setup:
### 3.1 Fetch the query identifier/s

Retrieve the `DataCache` object and read the values for:
`DataCache.dbtrId`
`DataCache.cdtrId`
`DataCache.dbtrAcctId`
`DataCache.cdtrAcctId`

### 2.2 Other parameters for the query
Read the variables from the rule config:
`maxQueryRange` to limit the historical timeframe of the query
`maxQueryLimit` to limit the number of results returned
`tolerance` to accommodate inexact matches when evaluating thresholds
`evaluationIntervalTime` to set the size of intervals for time-based histograms
`minimumTransactions` to define the minimum number of transactions for a meaningful result

### 3.2 Parse the query

```typescript
Insert AQL query
```

The query string must be properly and securely parsed before being sent to Arango.

*Explain query composition*

## 3. Query execution

The parsed query string must be sent to Arango.
The query string must be sent to Arango via a wrapper function that is hosted in the query library.
Report any errors in the execution of the query as an ERROR rule result with an explicit error string as the reason code.

*Explain query result handling and error/exit conditions, e.g.*
If the `historicalAmounts` array in the query response is `null` or empty, abort with exit condition `.x01`.
If the `targetAmount` in the query response is `null` or empty there was an error that needs to be reported (2 above).

*Explain the expected query result and error/exit conditions*

## 4. Query post-processing

*Explain steps to process the query result to prepare the result for interpretation*

## 5. Determine rule results

Define the rule results via either the `determineOutcome` (banded) or a custom `exclusiveDetermineOutcome` (cased) function according to the rule config.

## 6. Return the `ruleResult`
