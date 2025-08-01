<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8" />
<title>タイトル付き複数メモ管理アプリ</title>
<style>
  body {
    font-family: monospace;
    margin: 2rem;
    background: #f9f9f9;
  }
  h1 {
    text-align: center;
    color: #333;
  }
  #memoList {
    margin-bottom: 1rem;
  }
  #memoList button {
    margin-right: 0.5rem;
    margin-bottom: 0.5rem;
    padding: 0.3rem 0.8rem;
    font-family: monospace;
    cursor: pointer;
  }
  #titleInput {
    width: 100%;
    font-size: 18px;
    padding: 0.4rem 0.6rem;
    margin-bottom: 0.5rem;
    border-radius: 6px;
    border: 1px solid #ccc;
    font-family: monospace;
    box-sizing: border-box;
  }
  textarea {
    width: 100%;
    height: 260px;
    padding: 1rem;
    font-size: 16px;
    font-family: monospace;
    border: 1px solid #ccc;
    border-radius: 8px;
    resize: vertical;
    box-sizing: border-box;
  }
  #controls {
    margin-top: 1rem;
    text-align: center;
  }
  button.controlBtn {
    padding: 0.7rem 1.5rem;
    margin: 0 0.5rem;
    font-family: monospace;
    border: none;
    background-color: #007bff;
    color: white;
    border-radius: 6px;
    cursor: pointer;
    transition: background-color 0.2s ease;
  }
  button.controlBtn:hover {
    background-color: #0056b3;
  }
</style>
</head>
<body>

<h1>📝 タイトル付き複数メモ管理アプリ</h1>

<div id="memoList"></div>

<input type="text" id="titleInput" placeholder="メモのタイトルを入力してください" />

<textarea id="memo" placeholder="ここにメモを書いてください..."></textarea>

<div id="controls">
  <button class="controlBtn" id="newMemoBtn">新規メモ</button>
  <button class="controlBtn" id="deleteMemoBtn">メモ削除</button>
</div>

<script>
  const STORAGE_KEY = 'multiMemoAppWithTitle';
  const memoListDiv = document.getElementById('memoList');
  const titleInput = document.getElementById('titleInput');
  const memoTextarea = document.getElementById('memo');
  const newMemoBtn = document.getElementById('newMemoBtn');
  const deleteMemoBtn = document.getElementById('deleteMemoBtn');

  let memos = {};
  let currentId = null;

  function loadMemos() {
    const data = localStorage.getItem(STORAGE_KEY);
    if (data) {
      memos = JSON.parse(data);
    } else {
      memos = {};
    }
  }

  function saveMemos() {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(memos));
  }

  function renderMemoList() {
    memoListDiv.innerHTML = '';
    Object.entries(memos).forEach(([id, memo]) => {
      const btn = document.createElement('button');
      btn.textContent = memo.title || '(無題)';
      btn.style.fontWeight = id === currentId ? 'bold' : 'normal';
      btn.title = memo.title || '(無題)';
      btn.onclick = () => {
        selectMemo(id);
      };
      memoListDiv.appendChild(btn);
    });
  }

  function selectMemo(id) {
    if (!memos[id]) return;
    currentId = id;
    titleInput.value = memos[id].title || '';
    memoTextarea.value = memos[id].content || '';
    renderMemoList();
  }

  function createNewMemo() {
    const id = 'memo_' + Date.now();
    memos[id] = { title: '新規メモ', content: '' };
    currentId = id;
    saveMemos();
    renderMemoList();
    selectMemo(id);
    titleInput.focus();
  }

  function deleteCurrentMemo() {
    if (!currentId) return alert('削除するメモがありません');
    if (!confirm(`「${memos[currentId].title}」を削除してもよろしいですか？`)) return;
    delete memos[currentId];
    currentId = null;
    saveMemos();
    renderMemoList();
    titleInput.value = '';
    memoTextarea.value = '';
  }

  // タイトル変更時に保存
  titleInput.addEventListener('input', () => {
    if (!currentId) return;
    memos[currentId].title = titleInput.value.trim() || '(無題)';
    saveMemos();
    renderMemoList();
  });

  // メモ変更時に保存
  memoTextarea.addEventListener('input', () => {
    if (!currentId) return;
    memos[currentId].content = memoTextarea.value;
    saveMemos();
  });

  // 初期化
  loadMemos();
  renderMemoList();

  if (Object.keys(memos).length > 0) {
    currentId = Object.keys(memos)[0];
    selectMemo(currentId);
  } else {
    createNewMemo();
  }

  newMemoBtn.addEventListener('click', createNewMemo);
  deleteMemoBtn.addEventListener('click', deleteCurrentMemo);
</script>

</body>
</html>
