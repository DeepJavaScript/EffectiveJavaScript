# 條款 62 使用巢狀或具名的回呼函式來進行非同步定序

**戡誤**

    Item 62
    A couple broken function references (reported by Alexandre Abreu):

    downloadABC calls downloadFiles23 but it should call downloadBC
    downloadBC calls downloadFile3 but it should call downloadC