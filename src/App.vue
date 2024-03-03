<script setup>
import axios from "axios";
import CryptoJS from "crypto-js";
import moment from "moment";
import { computed, onMounted, onUnmounted, ref } from "vue";

const paymentMessage = ref(null);

// nicepaySubmit 함수를 window 객체에 할당하여 전역에서 접근 가능하게 만듭니다.
function nicepaySubmit() {
  console.log("폼 제출 시작");
  console.log("폼 제출 명령 실행됨");
  document.payForm.submit();
}

function nicepayClose() {
  alert("결제가 취소 되었습니다");
}

onMounted(() => {
  // 컴포넌트가 마운트될 때 window 객체에 nicepaySubmit 함수를 할당합니다.
  window.nicepaySubmit = nicepaySubmit;
  window.nicepayClose = nicepayClose;
});

onUnmounted(() => {
  // 컴포넌트가 언마운트될 때 window 객체에서 nicepaySubmit 함수를 제거합니다.
  delete window.nicepaySubmit;
  delete window.nicepayClose;
});

const merchantKey =
  "EYzu8jGGMfqaDEp76gSckuvnaHHu+bC4opsSN6lHv3b2lurNYkVXrZ7Z1AoqQnXI3eLuaUFyoRNC6FkrzVjceg==";
const merchantID = "nicepay00m";
const ediDate = moment().format("YYYYMMDDHHmmss");
const amt = ref("1004");
const returnURL = "";
const goodsName = ref("나이스상품");
const moid = ref("nice_api_test_3.0");
const buyerName = ref("구매자");
const buyerEmail = ref("happy@day.com");
const buyerTel = ref("00000000000");

const count = ref(0);

const hashString = computed(() => {
  const str = `${ediDate}${merchantID}${amt.value}${merchantKey}`;
  return CryptoJS.SHA256(str).toString(CryptoJS.enc.Hex);
});

async function nicepayStart(event) {
  event.preventDefault();
  goPay(document.payForm);
}

async function test() {
  try {
    const response = await axios.get("/payment/test");
    console.log("test get  요청", response.data);
  } catch (error) {
    console.error("결제 요청 실패:", error);
  }
}
</script>
<template>
  <body>
    <form
      name="payForm"
      method="post"
      action="http://localhost:3000/payment/authReq"
      accept-charset="euc-kr"
    >
      <table>
        <tr>
          <th>PayMethod</th>
          <td><input type="text" name="PayMethod" v-model="payMethod" /></td>
        </tr>
        <tr>
          <th>GoodsName</th>
          <td><input type="text" name="GoodsName" v-model="goodsName" /></td>
        </tr>
        <tr>
          <th>Amt</th>
          <td><input type="text" name="Amt" v-model="amt" /></td>
        </tr>
        <tr>
          <th>MID</th>
          <td><input type="text" name="MID" v-model="merchantID" /></td>
        </tr>
        <tr>
          <th>Moid</th>
          <td><input type="text" name="Moid" v-model="moid" /></td>
        </tr>
        <tr>
          <th>BuyerName</th>
          <td><input type="text" name="BuyerName" v-model="buyerName" /></td>
        </tr>
        <tr>
          <th>BuyerEmail</th>
          <td>
            <input type="text" name="BuyerEmail" v-model="buyerEmail" />
          </td>
        </tr>
        <tr>
          <th>BuyerTel</th>
          <td><input type="text" name="BuyerTel" v-model="buyerTel" /></td>
        </tr>
        <tr>
          <th>ReturnURL [Mobile only]</th>
          <td><input type="text" name="ReturnURL" v-model="returnURL" /></td>
        </tr>
        <tr>
          <th>Virtual Account Expiration Date(YYYYMMDD)</th>
          <td><input type="text" name="VbankExpDate" value="" /></td>
        </tr>

        <input type="hidden" name="NpLang" value="KO" />
        <!-- EN:English, CN:Chinese, KO:Korean -->
        <input type="hidden" name="GoodsCl" value="1" />
        <!-- products(1), contents(0)) -->
        <input type="hidden" name="TransType" value="0" />
        <!-- USE escrow false(0)/true(1) -->
        <input type="hidden" name="CharSet" value="utf-8" />
        <!-- Return CharSet -->
        <input type="hidden" name="ReqReserved" value="" />
        <!-- mall custom field -->

        <!-- DO NOT CHANGE -->
        <input type="hidden" name="EdiDate" v-model="ediDate" />
        <!-- YYYYMMDDHHMISS -->
        <input type="hidden" name="SignData" v-model="hashString" />
        <!-- EncryptData -->
      </table>
      <a href="#" class="btn_blue" @click="nicepayStart">REQUEST</a>
      <a href="#" class="btn_blue" @click="test">test</a>
    </form>
  </body>
</template>


<style scoped>
</style>
