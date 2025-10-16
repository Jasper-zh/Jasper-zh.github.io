---
title: tools
date: 2022-09-01 22:04:23
type: "tools"
layout: false
---

<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>工具 - 在线录屏</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            font-family: Arial, sans-serif;
        }
        .start-screen-recording-mid {
            margin: 50px;
        }
        .rec-dot {
            display: inline-block;
            width: 12px;
            height: 12px;
            background-color: red;
            border-radius: 50%;
            margin-right: 8px;
            animation: blink 1.5s infinite;
        }
        @keyframes blink {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.3; }
        }
    </style>
</head>
<body>
    <div style="margin: 0px 0 0 50px;" class="start-screen-recording-mid">
        <div>
            <div class="rec-dot"></div><span>在线录屏</span>
        </div>
    </div>
    <script src="https://yournotes.oss-cn-beijing.aliyuncs.com/tools/xx.js"></script>
</body>
</html>