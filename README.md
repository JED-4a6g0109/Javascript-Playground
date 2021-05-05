<!DOCTYPE html>
<html>

<head>
    <title>拼圖遊戲</title>
    <style>
        body {
            background-image: url("https://github.com/JED-4a6g0109/public-project/blob/master/image.png?raw=true");
        }

        .game {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            box-shadow: 0 1px 4px (0, 0, 0, 0.5);
            padding: 15px;
            background-color: rgb(255, 255, 255);
            border-radius: 5px;
        }

        .grid {
            display: grid;
            grid-template-columns: 80px 80px 80px 80px;
            grid-template-rows: 80px 80px 80px 80px;
            border: 1px solid black;
        }

        .grid button {
            background-color: wheat;
            color: black;
            font-size: 25px;
            font-weight: bold;
            border: 1px solid black;
        }

        .footer {
            margin-top: 15px;
            display: flex;
            justify-content: space-between;
        }

        .footer input {
            border: none;
            font-size: 20px;
            font-weight: bold;
            border-radius: 5px;
            box-shadow: 0 1px 4px (0, 0, 0, 0.5);
            padding: 5px;
            width: 80px;
            background-color: black;
            color: white;
        }
        .area{
            border: 1px solid black;
            width: 160px;
        }

        .footer span {
            flex: 1px;
            text-align: right;
            font-size: 20px;
            color: black;
            font-weight: bold;
            margin: auto 0;
        }
    </style>

    <script>
        var new_array = [];
        var answer_array = [];

        let range = 0;
        let move_count = 0;

        //遊戲開始
        window.onload = function () {
            const btn = document.querySelector('#btn');
            btn.onclick = function () {
                move_count = 0;
                document.getElementById('move').innerHTML = `Move:${move_count}`

                const rbs = document.querySelectorAll('input[name="select"]');
                let selectedValue;
                for (const rb of rbs) {
                    if (rb.checked) {
                        selectedValue = rb.value;
                        break;
                    }
                }
                hard(selectedValue);
                range = selectedValue;
            };
        }


        //難度選擇
        function hard(select) {
            var diff = select;

            remove_button()

            if (select == "3") {
                document.getElementById("grid").setAttribute("style", "grid-template-columns: 80px 80px 80px; grid-template-rows: 80px 80px 80px;");
                document.getElementById("grid").setAttribute("style", "grid-template-columns: 80px 80px 80px; grid-template-rows: 80px 80px 80px;");
                init_button(3)

            }
            else if (select == "5") {
                document.getElementById("grid").setAttribute("style", "grid-template-columns: 80px 80px 80px 80px 80px; grid-template-rows: 80px 80px 80px 80px 80px;");
                document.getElementById("grid").setAttribute("style", "grid-template-columns: 80px 80px 80px 80px 80px; grid-template-rows: 80px 80px 80px 80px 80px;");
                init_button(5)

            }
            else {
                document.getElementById("grid").setAttribute("style", "grid-template-columns: 80px 80px 80px 80px 80px 80px 80px; grid-template-rows: 80px 80px 80px 80px 80px 80px 80px;");
                document.getElementById("grid").setAttribute("style", "grid-template-columns: 80px 80px 80px 80px 80px 80px 80px; grid-template-rows: 80px 80px 80px 80px 80px 80px 80px;");
                init_button(7)

            }
        }

        //清空所有按鈕
        function remove_button() {
            const removebutton = document.getElementById("grid");
            removebutton.innerHTML = '';
        }


        //未洗牌
        function init_button(count) {
            let square = count * count
            var space = square - 1
            var init_array = [];

            for (var i = 1; i <= square; i++) {
                if (i == square) {
                    init_array.push(0)
                    answer_array.push(0)
                } else {
                    init_array.push(i)
                    answer_array.push(i)
                }

            }


            shuffle_array = shuffle(init_array)

            create_button(shuffle_array)

            new_array = dimension_convert(shuffle_array, count)



        }

        //按鈕建立
        function create_button(shuffle_array) {
            for (j = 0; j < shuffle_array.length; j++) {
                var container = document.getElementById('grid');
                var button = document.createElement('button');

                if (shuffle_array[j] == 0) {
                    button.setAttribute("id", 0);
                    button.setAttribute("onclick", "push(this.id)");
                    button.innerText = "";

                } else {
                    button.setAttribute("id", shuffle_array[j]);
                    button.setAttribute("onclick", "push(this.id)");

                    button.innerText = shuffle_array[j];
                }
                container.appendChild(button);
            }
        }



        //二維降成一維，為了方便確定是否完成好比對
        function dimension_convert(array, split) {

            const newArr = [];
            while (array.length) newArr.push(array.splice(0, split));
            return newArr

        }

        //洗牌
        function shuffle(arr) {
            arr.sort(() => Math.random() - 0.5)
            return arr
        }

        //按鈕按下開始傳入id，並尋找2d array (x,y)
        function push(clicked_id) {
            let push_number = clicked_id
            let zore_point,change;
            let stop =0;
            for (var i = 0; i < new_array.length; i++) {

                for (var j = 0; j < new_array[i].length; j++) {

                    if (push_number == new_array[i][j]) {
                        zore_point = find_point(i, j)
                        if((zore_point[0] != -1 && zore_point[1] != -1) && stop<1){ 
                            change = new_array[i][j]
                            new_array[zore_point[0]][zore_point[1]] = new_array[i][j]
                            new_array[i][j] = 0
                            stop++;
                            move_count++;
                            document.getElementById('move').innerHTML = `Move:${move_count}`
                        }

                    }

                }
            }
            remove_button(range)
            console.log("結束陣列")
            Onedimensional = reduce_array(new_array)
            console.log(Onedimensional)
            console.log(answer_array)

            status = done(Onedimensional)
            create_button(Onedimensional)


        }

        //一維判斷是否完成拼圖
        function done(array){
            if(JSON.stringify(array)==JSON.stringify(answer_array)){
                document.getElementById('done').innerHTML = "過關!"
                return "done"
            }
            
        }


        //前後左右演算法，尋找是否有為0的陣列在附近
        function find_point(i, j) {
            const pass = null;


            if (i + 1 < range && new_array[i + 1][j] == 0) {//right
                return [i + 1, j]


            } else if (i - 1 >= 0 && new_array[i - 1][j] == 0) {//left
                return [i - 1, j]

            } else if (j + 1 < range && new_array[i][j + 1] == 0) {//top
                return [i, j + 1]

            } else if (j - 1 >= 0 && new_array[i][j - 1] == 0) {//bottom
                return [i, j - 1]

            } else {
                return [-1,-1]
            }

        }

        function reduce_array(arr) {
            return Array.prototype.concat.apply([], arr)
        }

    </script>
</head>

<body>
    <div class="game">
        <div class="grid" id="grid">

        </div>
        <form name="Difficulty">
            <p>難度選擇</p>
            <div class = area>
                <input type="radio" name="select" checked="checked" value="3">3X3
                <input type="radio" name="select" value="5">5X5
                <input type="radio" name="select" value="7">7X7
            </div>


        </form>

        <div class="footer">
            <input type="button" id="btn" value="Play">

            <span id="move">Move: 0</span>
            <span id="done"></span>

        </div>


    </div>
</body>


</html>
