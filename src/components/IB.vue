<script setup lang="ts">
import { ref,watch } from 'vue'

const props = defineProps<{ transactionAmount: number }>();

type Fees = {
  commissionFee?:number; // 佣金费用 (0.06%*总交易额)
  transactionFee?:number; // 交易费 (0.005%*总交易额 + 0.5 港币)
  settlementFee?:number; // 结算交收费 (0.002%*总交易额，最低 2 港币，最高 100 港币)、
  transactionLevy?:number; // 交易征费 (0.0027%*总交易额)
  stampDuty?:number; // 印花税 (0.1%*总交易额，不足 1 港币作 1 港币计算)
  financialReportingLevy?:number; // 财匯局交易征费 (0.00015%*总交易额)
  totalFee?:number; // 汇总总费用
}


function calculateFees(transactionAmount: number):Fees {
  // 确保交易金额为正数
  if (transactionAmount <= 0) {
    throw new Error("交易金额必须大于零");
  }

  // 辅助函数：保留两位小数
  const roundToTwo = (num: number) => Math.round(num * 100) / 100;

  const fees:Fees = {};

  // 佣金费用 (0.06%*总交易额)
  fees.commissionFee = roundToTwo(0.0006 * transactionAmount);

  // 交易费 (0.005%*总交易额 + 0.5 港币)
  fees.transactionFee = roundToTwo((0.00005 * transactionAmount) + 0.5);

  // 结算交收费 (0.002%*总交易额，最低 2 港币，最高 100 港币)
  const settlementFee = 0.00002 * transactionAmount;
  fees.settlementFee = roundToTwo(Math.min(Math.max(settlementFee, 2), 100));

  // 交易征费 (0.0027%*总交易额)
  fees.transactionLevy = roundToTwo(0.000027 * transactionAmount);

  // 印花税 (0.1%*总交易额，不足 1 港币作 1 港币计算)
  fees.stampDuty = roundToTwo(Math.ceil(0.001 * transactionAmount));

  // 财汇局交易征费 (0.00015%*总交易额)
  fees.financialReportingLevy = roundToTwo(0.0000015 * transactionAmount);

  // 汇总总费用
  fees.totalFee = roundToTwo(
    fees.commissionFee +
    fees.transactionFee +
    fees.settlementFee +
    fees.transactionLevy +
    fees.stampDuty +
    fees.financialReportingLevy
  );

  return fees;
}

const fees= ref<Fees>(calculateFees(props.transactionAmount))



watch(
  () => props.transactionAmount,
  (newAmount) => {
    fees.value = calculateFees(newAmount)
  }
);

</script>

<template>
  <div style="text-align: left;">
    <h3>盈透</h3>
    
    <table>
      <tbody>
      <tr>
        <td>费用项:</td>
        <td>金额</td>
        <td>费用说明</td>
      </tr>
      <tr>
        <td>佣金:</td>
        <td>{{fees.commissionFee}}</td>
        <td>0.06%*总交易额</td>
      </tr>
      <tr>
        <td>交易费:</td>
        <td>{{fees.transactionFee}}</td>
        <td>0.005%*总交易额 + 0.5 港币</td>
      </tr>

      <tr>
        <td>结算交收费:</td>
        <td>{{fees.settlementFee}}</td>
        <td>0.002%*总交易额，最低 2 港币，最高 100 港币</td>
      </tr>

      <tr>
        <td>交易征费:</td>
        <td>{{fees.transactionLevy}}</td>
        <td>0.0027%*总交易额</td>
      </tr>

      <tr>
        <td>印花税:</td>
        <td>{{fees.stampDuty}}</td>
        <td>0.1%*总交易额，不足 1 港币作 1 港币计算</td>
      </tr>

      <tr>
        <td>财汇局交易征费:</td>
        <td>{{fees.financialReportingLevy}}</td>
        <td>0.00015%*总交易额</td>
      </tr>
      <tr>
        <td>总费用:</td>
        <td>{{fees.totalFee}}</td>
        <td></td>
      </tr>
      </tbody>
    </table>
  </div>
  
</template>

<style scoped>
.read-the-docs {
  color: #888;
}

table{
  border-collapse: collapse;
}

td{
  border: 1px solid #ddd;
  /* border-spacing: 0; */
  padding-left: 5px;
  padding-right: 5px;
  
}

</style>
