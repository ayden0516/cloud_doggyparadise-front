<template>
  <main class="container">
    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th scope="col">訂單ID</th>
          <th scope="col">訂購日期</th>
          <th scope="col">狀態</th>
          <th scope="col">總金額</th>
          <th scope="col">付款方式</th>
          <th scope="col"></th>
          <th scope="col"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(o, index) in orders" :key="index">
          <td>{{ o.orderId }}</td>
          {{
            dateFormat(o.orderDate)
          }}
          <td>{{ date }}</td>
          {{
            paymentMethod(o.paymentMethod)
          }}
          <td>{{ payMethod }}</td>

          <td>{{ o.totalPrice }}</td>
          {{
            paymentStatus(o.paymentStatus)
          }}
          <td>{{ payStatus }}</td>
          <td>
            <router-link :to="'/profile/order/' + o.orderId + '/orderdetails'">
              <button type="button" class="btn btn-success">查看詳細</button>
            </router-link>
          </td>
          <td>
            <button
              id="cancel"
              name="cancel"
              type="button"
              class="btn btn-primary"
              data-bs-toggle="modal"
              data-bs-target="#staticBackdrop"
              @click="setOrderId(o.orderId)"
            >
              取消訂單
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <div
      class="modal fade"
      id="staticBackdrop"
      data-bs-backdrop="static"
      data-bs-keyboard="false"
      tabindex="-1"
      aria-labelledby="staticBackdropLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="staticBackdropLabel">系統訊息</h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">要取消該筆訂單？</div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              否
            </button>
            <button
              type="button"
              class="btn btn-primary"
              data-bs-dismiss="modal"
              @click="orderCancel"
            >
              <!--cancelOrder-->
              確定
            </button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>
<script>
import { useMemberStore } from "@/stores/memberStore";
import axios from "axios";
import { emptyProps } from "element-plus";
export default {
  data() {
    return {
      memberId: "",
      memberName: "",
      orders: [],
      date: "",
      payMethod: "",
      payStatus: "",
      activeOrderId: "",
    };
  },
  mounted() {
    const memberStore = useMemberStore();
    this.memberId = memberStore.memberId;
    this.memberName = memberStore.memberName;

    axios.get(`${this.API_URL}/order`).then((re) => {
      this.orders = re.data;
      console.log(this.orders);
    });
  },
  methods: {
    dateFormat(date) {
      let dateObj = new Date(date);
      this.date = dateObj.toLocaleString("zh-TW", {
        year: "numeric",
        month: "2-digit",
        day: "2-digit",
        hour: "2-digit",
        minute: "2-digit",
        second: "2-digit",
      });
    },
    paymentMethod(value) {
      switch (true) {
        case value === 0:
          this.payMethod = "LinePay";
          break;
        case value === 1:
          this.payMethod = "貨到付款";
          break;
      }
    },
    paymentStatus(value) {
      switch (true) {
        case value === 0:
          this.payStatus = "未付款";
          break;
        case value === 1:
          this.payStatus = "已付款";
          break;
        case value === 2:
          this.payStatus = "已取消，未退";
          break;
        case value === 3:
          this.payStatus = "完成退款";
          break;
        case value === 4:
          this.payStatus = "已取消，不須退款";
          break;
      }
    },
    setOrderId(value) {
      this.activeOrderId = value;
      console.log(this.activeOrderId);
    },
    orderCancel() {
      const formData = new FormData();
      formData.append("orderId", this.activeOrderId);
      axios
        .post(`${this.API_URL}/order/doOrderCancel`, formData)
        .then((response) => {
          location.reload();
        });
    },
  },
};
</script>
<style>
router-link {
  display: table-row;
  text-decoration: none;
  /* 添加其他必要的样式 */
}
</style>
