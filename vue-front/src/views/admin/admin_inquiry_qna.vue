<template>
  <div>      
  <div class="article-list table-common con" style="margin-bottom: 20px;">
    <!-- 테이블 높이를 너비 100%로 수정 -->
      <table style='width:100%;table-layout:fixed;'> 
          <thead>
              <tr>
                  <th scope="col">번호</th>
                  <th scope="col">문의유형</th>
                  <th scope="col">제목</th>
                  <th scope="col">작성자</th>
                  <th scope="col">작성일</th>
                  <th scope="col">문의상태</th>
              </tr>
          </thead>
          <tbody>
              <tr v-for="(qna,i) in qnalist" :key="i">
                  <td>{{ pageNum * pageSize + i + 1 }}</td>
                  <td>{{category(qna.INQUIRY_CATEGORY)}}</td>
                  <td @click="goToContent(qna.INQUIRY_NUMBER)" style="cursor:pointer;">{{qna.INQUIRY_TITLE}}</td>
                  <td>{{qna.INQUIRY_MEMBER_EMAIL}}</td>
                  <td>{{qna.INQUIRY_DATE.split('T')[0]}}</td>
                  <td v-if="qna.INQUIRY_ANSWER != null">답변완료</td>
                  <td v-else>답변중</td> 
              </tr>
             
          </tbody>
      </table>
  </div>
<br/>
           

</div>

</template>
<script>
  import axios from 'axios';

  export default {
      data() {
          return {
              qnalist: [],
              pageSize: 5, //한페이지에 보여지는 정보 갯수
              pageNum: 0,
              currentPage: 0, //현재 게시판의 총 글 개수
          }
      },  
      computed: {
  // user() {
  //   return this.$store.state.member; //로그인 확인
  // },
          totalpage() {
              if(this.currentPage == 0) { // 현재 게시판 글 갯수가 0개일때 총 페이지가 0이 되는거 방지
              return 1; 
          }else {
              return Math.ceil(this.currentPage/10); // (글 갯수/10)한 후 올림 연산을 통해 총 페이지 계산
          }
          }
      },
      mounted() {
          axios({
              url: "http://localhost:3000/qna/admin_inquiry",
              method: "GET",
              data: { 
                  page: this.$route.query.page,
                  pagesize: this.pageSize
              },
          }).then(res => {
              this.qnalist = res.data;
          }).catch(err => {
              alert("게시물을 불러오는 동안 에러 발생: " + err);
          });
          //현재 게시판 글 개수 가져오는 axios작성
      },
      methods: {
          category(category) {
              switch(category) {
                  case 1:
                      return '[결제]';
                      break;
                  case 2:
                      return '[교환/환불]';
                      break;
                  case 3:
                      return '[배송]';
                      break;
                  default:
                      return '[입고/기타]';
                      break;
              }
          },
          goToContent(INQUIRY_NUMBER) {
              window.location.href = window.location.pathname + '/qnacontent?INQUIRY_NUMBER=' + INQUIRY_NUMBER
              //window.location.pathname은 localhost:8080/admin/admin_inquiry_qna 임
  },
      }
  }
</script>

