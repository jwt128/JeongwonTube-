<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>JeongwonTube 가상클립보드</title>
<style>
  @import url('https://cdn.rawgit.com/moonspam/NanumSquare/master/nanumsquare.css');
  body {
    font-family: 'NanumSquare', sans-serif;
    background-color: #f8f9fa; /* 배경색 추가 */
    margin: 0;
    padding: 0;
  }
  .container {
    width: 80%;
    margin: auto;
    background-color: #ffffff; /* 박스 배경색 */
    padding: 20px;
    border-radius: 10px; /* 박스 모서리 둥글게 */
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1); /* 그림자 추가 */
  }
  h1 {
    text-align: center; /* 제목 가운데 정렬 */
  }
  .input-box, .list-box {
    background-color: #f0f0f0;
    padding: 20px;
    margin-top: 20px;
    border-radius: 5px; /* 입력 박스와 리스트 박스 모서리 둥글게 */
  }
  input[type="text"] {
    width: calc(100% - 80px); /* 입력창 너비 조정 */
    padding: 10px;
    border-radius: 5px;
    border: 1px solid #ccc;
  }
  button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    background-color: #007bff;
    color: #ffffff;
    cursor: pointer;
  }
  button:hover {
    background-color: #0056b3; /* 버튼 호버 시 색상 변경 */
  }
  table {
    width: 100%;
    border-collapse: collapse; /* 테이블 경계선 제거 */
    margin-top: 10px;
  }
  th, td {
    padding: 10px;
    text-align: left;
    border-bottom: 1px solid #ddd;
  }
  th {
    background-color: #f0f0f0; /* 헤더 배경색 추가 */
  }
  .copy-button, .delete-button {
    padding: 5px 10px;
    border: none;
    border-radius: 3px;
    cursor: pointer;
  }
  .copy-button {
    background-color: #28a745; /* 복사 버튼 색상 변경 */
    color: #ffffff;
    margin-right: 5px;
  }
  .delete-button {
    background-color: #dc3545; /* 삭제 버튼 색상 변경 */
    color: #ffffff;
  }
</style>
</head>
<body>

<div class="container">
  <h1>JeongwonTube 가상클립보드</h1>
  <div class="input-box">
    <input type="text" id="textInput" placeholder="텍스트를 입력하세요"><p></p>
    <button onclick="saveText()">텍스트 저장</button>   
    <input type="file" id="fileInput">
    <button onclick="uploadFile()">파일 업로드</button>
    <button onclick="deleteAllText()">전체 삭제</button>
  </div>
  <div class="list-box">
    <table id="textList">
      <!-- 여기에 텍스트 리스트가 표시됩니다 -->
    </table>
  </div>
</div>

<script>
  // 페이지 로딩 시 저장된 텍스트 불러오기
  window.onload = function() {
    displayTexts();
  };

  // 텍스트 저장 함수
  function saveText() {
    const text = document.getElementById('textInput').value;
    if (!text) {
      alert('텍스트를 입력하세요.');
      return;
    }
    let texts = localStorage.getItem('texts');
    texts = texts ? JSON.parse(texts) : [];
    texts.push(text);
    localStorage.setItem('texts', JSON.stringify(texts));
    displayTexts();
    alert('텍스트가 저장되었습니다.');
    document.getElementById('textInput').value = ''; // 입력창 초기화
  }

  // 파일 업로드 함수
  function uploadFile() {
    const fileInput = document.getElementById('fileInput');
    const file = fileInput.files[0];
    if (!file) {
      alert('파일을 선택하세요.');
      return;
    }
    const fileType = file.type.split('/')[0];
    if (fileType === 'image') {
      // 이미지 업로드 시 이미지 주소 복사
      const imageURL = URL.createObjectURL(file);
      navigator.clipboard.writeText(imageURL).then(function() {
        alert('이미지 주소가 복사되었습니다.');
      }, function(err) {
        console.error('복사 실패:', err);
      });
    } else {
      // 기타 파일 업로드 시 파일 주소 복사
      const fileURL = window.URL.createObjectURL(file);
      navigator.clipboard.writeText(fileURL).then(function() {
        alert('파일 주소가 복사되었습니다.');
      }, function(err) {
        console.error('복사 실패:', err);
      });
    }
    fileInput.value = ''; // 파일 인풋 초기화
  }

  // 저장된 텍스트 표시 함수
  function displayTexts() {
    const texts = JSON.parse(localStorage.getItem('texts') || '[]');
    const table = document.getElementById('textList');
    table.innerHTML = '<tr><th>저장된 텍스트</th><th>작업</th></tr>'; // 헤더 초기화
    texts.forEach(function(text, index) {
      const row = table.insertRow(-1);
      const cell1 = row.insertCell(0);
      const cell2 = row.insertCell(1);
      cell1.innerHTML = text;
      cell2.innerHTML = '<button class="copy-button" onclick="copyText(' + index + ')">복사</button>' + 
                        '<button class="delete-button" onclick="deleteText(' + index + ')">삭제</button>'; // 클래스 추가
    });
  }

  // 텍스트 복사 함수
  function copyText(index) {
    const texts = JSON.parse(localStorage.getItem('texts') || '[]');
    navigator.clipboard.writeText(texts[index]).then(function() {
      alert('텍스트가 클립보드에 복사되었습니다.');
    }, function(err) {
      console.error('복사 실패:', err);
    });
  }

  // 텍스트 삭제 함수
  function deleteText(index) {
    let texts = JSON.parse(localStorage.getItem('texts') || '[]');
    texts.splice(index, 1); // 해당 인덱스의 텍스트 삭제
    localStorage.setItem('texts', JSON.stringify(texts)); // 삭제된 리스트를 다시 저장
    displayTexts(); // 변경된 리스트 다시 표시
    alert('텍스트가 삭제되었습니다.');
  }

  // 전체 텍스트 삭제 함수
  function deleteAllText() {
    localStorage.removeItem('texts'); // 모든 텍스트 삭제
    displayTexts(); // 변경된 리스트 다시 표시
    alert('모든 텍스트가 삭제되었습니다.');
  }
</script>
</body>
</html>
