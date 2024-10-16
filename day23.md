# Day 23

## Created bid.ejs

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AuctionBay</title>
    <link href="/src/input.css" type="text/css" rel="stylesheet">
    <link href="/src/output.css" type="text/css" rel="stylesheet">
    <style>
        button {
            transform: scale(1);
            transition: transform 150ms ease-in-out;
        }

        button:active {
            transform: scale(0.95);
        }

        .num-button{
            background: linear-gradient(to bottom, #15803d, #14532d);
            border-radius: 0.125rem;
        }
        .suggestion-btn{
            background: linear-gradient(to bottom, #0284c7, #0369a1);
            border-radius: 6px;
        }
        table {
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid #000000;
        }
        th {
            text-align: left;
        }
    </style>
</head>
<body class="text-white font-body_font flex">
    <div class="absolute top-0 z-[-2] min-h-screen h-auto w-full flex flex-col items-center bg-[#000000] bg-[radial-gradient(#ffffff33_1px,#00091d_1px)] bg-[size:20px_20px]">
        <div class="flex lg:py-14 p-5 lg:px-20 justify-between items-center w-full text-yellow-400">
            <div class="lg:text-4xl text-base flex lg:flex-row flex-col lg:gap-2 gap-0">Starting Amount: <div><span class="lg:text-4xl text-lg py-0">&#8377;</span><%= price %></div></div>
            <div id="timer" class="lg:text-4xl text-3xl">5:00</div>
            <div id="currentBid" class="lg:text-4xl text-base flex lg:flex-row flex-col lg:gap-2 gap-0">Current Bid: <div><span class="lg:text-4xl text-lg py-0">&#8377;</span>0</div></div>
        </div>
        <div class="flex lg:flex-row flex-col lg:justify-between lg:items-start justify-center items-center lg:gap-4 gap-2 w-full lg:px-20 px-7">
            <div class="text-white lg:text-2xl text-base lg:w-[900px] w-[350px]">
                <table>
                    <thead class="bg-violet-900">
                        <tr>
                            <th class="lg:w-[500px] w-[230px] lg:p-2 p-1">Name</th>
                            <th class="lg:w-[300px] w-[120px] lg:p-2 p-1">Value</th>
                        </tr>
                    </thead>
                    <tbody class="bg-gray-800" id="bidTableBody">
                        
                    </tbody>
                </table>
            </div>
            <div id="numpad" class="flex flex-col lg:gap-4 gap-3 justify-center items-center lg:w-[400px] w-[350px]">
                <span class="lg:text-2xl text-base">Bid suggestions</span>
                <div id="suggestions" class="flex justify-center items-center lg:gap-4 gap-2"></div>
                <input type="number" id="bidValue" autofocus class="text-black lg:p-2 p-1 lg:w-[250px] w-[150px] rounded-lg lg:text-2xl text-base">
                <div id="numberPad" class="flex lg:flex-col flex-row-reverse lg:flex-nowrap flex-wrap-reverse items-center justify-center lg:gap-4 gap-3 lg:text-2xl text-lg">
                    <div class="flex justify-center items-center lg:gap-4 gap-3">
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="7">7</button>
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="8">8</button>
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="9">9</button>
                    </div>
                    <div class="flex justify-center items-center lg:gap-4 gap-3">
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="4">4</button>
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="5">5</button>
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="6">6</button>
                    </div>
                    <div class="flex justify-center items-center lg:gap-4 gap-3">
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="1">1</button>
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="2">2</button>
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="3">3</button>
                    </div>
                    <div class="flex justify-center items-center lg:gap-4 gap-3">
                        <button class="num-button lg:w-[50px] w-[35px] lg:p-2 p-1" data-value="0">0</button>
                    </div>
                </div>
                <div class="flex justify-center items-center lg:gap-4 gap-3 lg:text-2xl text-lg">
                    <button id="clear" class="bg-gradient-to-b from-sky-700 to-sky-900 lg:p-3 p-1 rounded-lg">Clear</button>
                    <button id="submitBid" class="bg-gradient-to-b from-sky-700 to-sky-900 lg:p-3 p-1 rounded-lg">Submit</button>
                </div>
              </div>
        </div>
    </div>

    <script>       

        const numButtons = document.querySelectorAll('.num-button');
        const bidValueInput = document.getElementById('bidValue');
            numButtons.forEach(button => {
            button.addEventListener('click', () => {
                bidValueInput.value += button.getAttribute('data-value');
            });
            });
            document.getElementById('clear').addEventListener('click', () => {
            bidValueInput.value = '';
        });

        document.getElementById('submitBid').addEventListener('click', async () => {
            const bidValue = parseFloat(document.getElementById('bidValue').value);
            const user_id = '<%= user_id %>';
            const bid_id = '<%= bid_id %>';
            const auction = '<%= auction %>';
            const price = '<%= price %>';
            const response = await fetch('/submit-bid', {
                method: 'POST',
                headers: {
            'Content-Type': 'application/json'
            },
            body: JSON.stringify({ user_id, bidValue, auction, bid_id, price })
            })
            const bidValueInput = document.getElementById('bidValue');
            bidValueInput.value = '';
            const result = await response.json();
            if (!response.ok) {
                alert(result.error);
            }
        });

        function countDigits(number) {
        const numberString = Math.abs(number).toString();
            return numberString.length;
        } 

        async function fetchBidsAndTimer() {
            const response = await fetch(`/bids-and-timer?bid_id=<%= bid_id %>&auction=<%= auction %>&price=<%= price %>`);
            const result = await response.json();
            if (!response.ok) {
                window.location.href = '/welcome';
                return;
            }
            const { rows, timer } = result;
            let {currentBid} = result
            const minutes = Math.floor(timer / 60);
            const seconds = timer % 60;
            document.getElementById('timer').innerText = `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
            currentBid = Number(currentBid)
            const bidTableBody = document.getElementById('bidTableBody');
            bidTableBody.innerHTML = '';
            rows.forEach(row => {
                const tr = document.createElement('tr');
                const tdEmail = document.createElement('td');
                const tdValue = document.createElement('td');
                tdEmail.innerText = row.username;
                tdValue.innerText = row.value;
                tdEmail.classList.add('lg:p-2', 'p-1');
                tdValue.classList.add('lg:p-2', 'p-1');
                tr.appendChild(tdEmail);
                tr.appendChild(tdValue);
                bidTableBody.appendChild(tr);
            });

            document.getElementById('currentBid').innerHTML = `Current Bid: <div><span class="lg:text-4xl text-lg py-0">&#8377;</span>${currentBid}</div>`;

            const auction = '<%= auction %>';
            const suggestions = document.getElementById('suggestions');
            const factor = Math.pow(10, (countDigits(currentBid)-2));
            if (auction === 'forward') {
                const suggestion1 = currentBid + factor;
                const suggestion2 = currentBid + (2 * factor);
                const suggestion3 = currentBid + (3 * factor);
                suggestions.innerHTML = `
                <button class="suggestion-btn lg:p-[6px] p-[2px]"><span class="lg:text-2xl text-base py-0">&#8377;</span><span class="lg:text-2xl text-base">${suggestion1}</span></button>
                <button class="suggestion-btn lg:p-[6px] p-[2px]"><span class="lg:text-2xl text-base py-0">&#8377;</span><span class="lg:text-2xl text-base">${suggestion2}</span></button>
                <button class="suggestion-btn lg:p-[6px] p-[2px]"><span class="lg:text-2xl text-base py-0">&#8377;</span><span class="lg:text-2xl text-base">${suggestion3}</span></button>
                `;
            } else {
                const suggestion1 = currentBid - factor;
                const suggestion2 = currentBid - (2 * factor);
                const suggestion3 = currentBid - (3 * factor);
                suggestions.innerHTML = `
                <button class="suggestion-btn lg:p-[6px] p-[2px]"><span class="lg:text-2xl text-base py-0">&#8377;</span><span class="lg:text-2xl text-base">${suggestion1}</span></button>
                <button class="suggestion-btn lg:p-[6px] p-[2px]"><span class="lg:text-2xl text-base py-0">&#8377;</span><span class="lg:text-2xl text-base">${suggestion2}</span></button>
                <button class="suggestion-btn lg:p-[6px] p-[2px]"><span class="lg:text-2xl text-base py-0">&#8377;</span><span class="lg:text-2xl text-base">${suggestion3}</span></button>
                `;
            }

            document.querySelectorAll('.suggestion-btn').forEach(button => {
                button.addEventListener('click', () => {
                    bidValueInput.value = button.innerText.replace('₹', '');
                });
            });  
        }
        // async function longPolling() {        
        //     await fetchBidsAndTimer();
        //     setTimeout(longPolling, 1000);
        // }
        // longPolling();
        setInterval(async () => {
            await fetchBidsAndTimer();
        }, 1000);

        document.addEventListener('DOMContentLoaded', async () => {
            await fetchBidsAndTimer();
        });

        window.addEventListener('beforeunload', function (e) {
            const confirmationMessage = 'Are you sure you want to leave this page?';
            (e || window.event).returnValue = confirmationMessage;
            window.location.href = '/welcome';
            return confirmationMessage;
        });

        

    </script>
</body>
</html>
```