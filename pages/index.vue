<template>
  <div>
    <main class="task-info__type">
      <div>
        <div class="left-side">
          <div class="block main_card">
            <p class="title text-bold">Выгрузка</p>
            <br />
            <p class="text-bold">Выполняет работу:</p>
            <p>- Собирает фотографии из заказов пользователей.</p>
            <p>- Выгружает по папкам.</p>
          </div>
        </div>

        <div class="right-side">
          <div id="info" class="notice" data-color="light-purple">
              Для того, чтобы посмотреть информацию о <span class="text-bold">выгрузке</span>, а также ее скачать, нажмите на требуемую выгрузку в столбце слева.
          </div>
          <div id="unload"/>
        </div>
      </div>
      
      <div id="cardsBlock" class="left-side"/>
    </main>
  </div>
</template>

<script>

function Card(status) {
  const card = document.createElement('div');
  card.classList.add("block", "card");

  if (status === 1) {
    card.classList.add('color-success');
  } else if (status === 2) {
    card.classList.add('color-danger');
  }

  return card;
}

function Unload(link) {
    const unload = document.getElementById("unload");

    const div = document.createElement("div");
    const el = document.createTextNode("Ссылка для скачивания архива Выгрузки (.zip):");
    div.classList.add("block", "card", "text-16", "text-bold");
    
    const divLink = document.createElement("div");
    const spanLink = document.createElement("a");
    spanLink.style.marginRight = "15px";
    spanLink.id = "link";
    divLink.classList.add("link");
    
    const elLink = document.createTextNode(link);
    spanLink.appendChild(elLink);
    divLink.appendChild(spanLink);
    
    const spanCopy = document.createElement("span");
    spanCopy.classList.add("span-link");
    spanCopy.id = "copy";
    spanCopy.addEventListener('click', () =>
    {
        const input = document.createElement("input");
        input.value = link;
        document.body.appendChild(input);

        input.select();
        document.execCommand("copy");
        document.body.removeChild(input);
    })
    const elCopy = document.createTextNode("скопировать ссылку");
    spanCopy.appendChild(elCopy);

    divLink.appendChild(spanCopy);

    div.appendChild(el);
    div.appendChild(divLink);

    unload.appendChild(div);
}

(async () => {
  const cardsBlock = document.getElementById("cardsBlock");

  const url = "https://dev-cabinet.seenday.com/e.scripts?page=pages:unload&event=get&unload_id=2175";
  const res = await useAPIFetch(url);

  const data = res.data._value;
  const json = JSON.parse(data);

  const dataArr = json.response.data;
  console.log(dataArr);

  for (let i = 0; i < dataArr.length; i++) {
    const newEl = new Card((dataArr[i].status === "green") ? 1 : ((dataArr[i].status === "red") ? 2 : ''))
    newEl.innerHTML +=
      dataArr[i].task_date +
      '<br/>Статус задачи: <span id="status" class="text-bold">' +
      dataArr[i].status_text +
      '</span><br/>ID выгрузки: <span id="status" class="text-bold">' +
      dataArr[i].id +
      "</span><br/>" +
      dataArr[i].event +
      '<br/>Размер загрузки: <span id="status" class="text-bold">' +
      dataArr[i].size +
      "</span><br/>";
    newEl.id = i;

    newEl.addEventListener('click', () =>
    {
        onClick();
        if (dataArr[i].download_link === undefined) {
            Unload("No link");
        } else {
            Unload(dataArr[i].download_link);
        }
    });

    cardsBlock.appendChild(newEl);
    }
    return res;
})();


function onClick() {
    const info = document.getElementById('info');
    if(!(info.style.display === "none"))
        info.style.display = "none";
};
</script>