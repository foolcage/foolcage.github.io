---
layout: post
title:  "fooltrader投资之财务指标"
categories: investing fooltrader
---

搞投资就要明明白白的搞,而所谓估值或者财务分析需要搞明白的问题不外乎:  
能用的资产有多少(assets)?负债多少(liabilities)?净资产多少(equity,book value)?  
收入多少(revenue)?支出多少(expense)?利润多少(profit)?  

# 1. 营业利润(operating profit) operatingProfit
```
营业利润=营业收入-营业成本-营业税金及附加-销售费用-管理费用-财务费用-资产减值损失+公允价值变动收益（损失的话用减）+投资收益

```
使用fooltrader来验证一下:

```python
def check_operating_profit(security_item):
    income_statement_list = get_income_statement_items(security_item=security_item)
    for income_statement in income_statement_list:
        operatingProfit = income_statement["operatingRevenue"] \
                          - income_statement["operatingCosts"] \
                          - income_statement["businessTaxesAndSurcharges"] \
                          - income_statement["sellingExpenses"] \
                          - income_statement["ManagingCosts"] \
                          - income_statement["financingExpenses"] \
                          - income_statement["assetsDevaluation"] \
                          + income_statement["incomeFromChangesInFairValue"] \
                          + income_statement["investmentIncome"]
        diff = operatingProfit - income_statement["operatingProfit"]
        if abs(diff) >= 1:
            logger.warn("{} operating profit calculating not pass,calculating result:{},report result:{}".format(
                income_statement['id'], operatingProfit, income_statement["operatingProfit"]))
        else:
            logger.info("{} operating profit calculating pass".format(income_statement['id']))

if __name__ == '__main__':
    check_operating_profit('000338')
```
# 2. 净利润(net profit) netProfit
```
净利润=利润总额-所得税费用
```
使用fooltrader来验证一下:
```
def check_net_profit(security_item):
    income_statement_list = get_income_statement_items(security_item=security_item)
    for income_statement in income_statement_list:
        netProfit = income_statement["totalProfits"] - income_statement["incomeTaxExpense"]
        diff = netProfit - income_statement["netProfit"]
        if abs(diff) >= 1:
            logger.warn("{} net profit calculating not pass,calculating result:{},report result:{}".format(
                income_statement['id'], netProfit, income_statement["netProfit"]))
        else:
            logger.info("{} net profit calculating pass".format(income_statement['id']))


if __name__ == '__main__':
    check_net_profit('000338')
```
# 3. 每股收益(earnings per share) EPS
```
EPS = (Net Income - Dividends on Preferred Stock)/Average Outstanding Shares
```
使用fooltrader来验证一下:
```
def check_eps(security_item):
    income_statement_list = get_income_statement_items(security_item=security_item)
    for income_statement in income_statement_list:
        balance_sheet = get_balance_sheet_items(security_item=security_item,
                                                report_period=income_statement['reportDate'])
        if not balance_sheet or balance_sheet['totalShareCapital'] == 0:
            continue

        eps = (income_statement["netProfit"] - income_statement["minorityInterestIncome"]) / (
            balance_sheet['totalShareCapital'])
        diff = eps - income_statement["EPS"]
        if abs(diff) >= 0.01:
            print("{} EPS calculating not pass,calculating result:{},report result:{}".format(
                income_statement['id'], eps, income_statement["EPS"]))
        else:
            print("{} EPS calculating pass".format(income_statement['id']))


if __name__ == '__main__':
    check_eps('000338')
```
# 4. 市盈率(price to earnings ratio) PE
```
PE = Price/EPS
```
# 5. 市销率( price to salesPS ratio) PS
```
PS = price/sales per share
```
# 6. 债务股本比(debt to equity ratio)
```
debt to equity ratio = total liabilities/bookValue(shareholders'equity)
```
# 7. 股息收益率(dividend yield)
```
dividend yield = annual dividend per share/price
```
# 8. 市净率(price to book ratio) PB
```
PB = price/bookValue per share
```
# 9. 股息支付率(dividend payout ratio)
```
dividend payout ratio = dividend per share/EPS
```
# 10. 流动比率(current ratio)CR
```
current ratio = current assets / current liabilities
```
