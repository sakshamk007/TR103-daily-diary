# Day 19

## Created postbid.ejs and /postbid route

### /postbid
```
router.get('/postbid', authenticate, (req,res)=>{
    const user_id = req.cookies.user_id;
    res.render('web/pages/postbid', {layout: "web/pages/postbid", user_id: user_id})
})

const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'public/uploads/');
    },
    filename: (req, file, cb) => {
        cb(null, Date.now() + path.extname(file.originalname));
    }
});
const upload = multer({ storage: storage });

router.post('/postbid', authenticate, upload.single('image'), async (req, res) => {
    const { name, user_id, email, title, auction, date, type, contact, description, price, time } = req.body;
    const image = req.file ? req.file.filename : null;
    const bid_id = uuidv4();
    try {
        const bidExists = await Bid.exists(user_id, title, name, description, auction);
        if (bidExists) {
            return res.status(400).render('web/layouts/auth', { page: 'error', status: 400, message: 'An auction with the same details already exists' });
        }
        await Bid.add(bid_id, user_id, name, email, title, auction, date, type, contact, description, price, time);
        if (image) {
            await Image.add(bid_id, image);
            res.status(200).render('web/layouts/auth', { page: 'success', status: 200, message: 'Auction details and image uploaded successfully' });
        } else {
            res.status(200).render('web/layouts/auth', { page: 'success', status: 200, message: 'Auction details uploaded successfully' });
        }
    } catch (err) {
        console.error('Error inserting bid:', err);
        res.status(500).render('web/layouts/auth', { page: 'error', status: 500, message: 'Error inserting auction' });
    }
});
```

### postbid.ejs

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AuctionBay</title>
    <link href="/src/input.css" type="text/css" rel="stylesheet">
    <link href="/src/output.css" type="text/css" rel="stylesheet">
    <!-- <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet"> -->
    <style>
        .modal {
            display: none;
            position: fixed;
            z-index: 50;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0, 0, 0, 0.8);
            justify-content: center;
            align-items: center;
        }
        .modal-content {
            background-color: #000423;
            color: #fff;
            padding: 20px;
            border: 1px solid white;
            height: 30%;
            max-height: 50%;
            overflow-y: auto;
        }
        .close {
            color: white;
            float: right;
            font-size: 40px;
            font-weight: bold;
        }
        .edit {
            color: white;
            float: right;
            font-size: 30px;
            font-weight: bold;
        }
        .close:hover,
        .close:focus, .edit:hover,
        .edit:focus {
            color: yellow;
            text-decoration: none;
            cursor: pointer;
        }
    </style>
</head>
<body class="text-white font-body_font flex">
    <div class="absolute top-0 z-[-2] h-auto w-auto min-h-full min-w-full flex flex-col items-center bg-[#000000] bg-[radial-gradient(#ffffff33_1px,#00091d_1px)] bg-[size:20px_20px]">
        <div class="lg:text-5xl text-3xl text-yellow-400 lg:p-14 p-7 text-center">&lt; Post &gt;</div>
        <div class="lg:px-14 px-7 lg:text-2xl text-base flex justify-center items-center pb-10">
            <form action="/postbid" method="post" enctype="multipart/form-data" class="flex flex-col lg:gap-10 gap-5 justify-center items-center" onsubmit="return validateContactNumber()">
                <div class="flex flex-col lg:gap-10 gap-5">
                    <div class="flex lg:flex-row flex-col lg:gap-16 gap-5 justify-between items-center">
                        <input type="hidden" name="user_id" value="<%= user_id %>">
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="name">Company Name</label>
                            <input type="text" name="name" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required value="<%= typeof name !== 'undefined' ? name : '' %>">
                        </div>  
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[250px]">
                            <label for="type">Company Type</label>
                            <input type="text" name="type" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required value="<%= typeof type !== 'undefined' ? type : '' %>">
                        </div>
                    </div>     
                    <div class="flex lg:flex-row flex-col lg:gap-16 gap-5 justify-between">
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="email">Email</label>
                            <input type="email" name="email" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required value="<%= typeof email !== 'undefined' ? email : '' %>">
                        </div>  
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="contact">Contact Number</label>
                            <input type="number" id="contact" name="contact" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" pattern="\d{10}" maxlength="10" required value="<%= typeof contact !== 'undefined' ? contact : '' %>">
                        </div>                 
                    </div>  
                    <div class="flex lg:flex-row flex-col lg:gap-16 gap-5 justify-between">
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="title">Job Title</label>
                            <input type="text" id="title" name="title" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required onclick="openModal('titleModal')" readonly value="<%= typeof title !== 'undefined' ? title : '' %>">
                        </div>
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="description">Job Description</label>
                            <input type="text" id="description" name="description" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required onclick="openModal('descriptionModal')" readonly value="<%= typeof description !== 'undefined' ? description : '' %>">
                        </div>                  
                    </div>   
                    <div class="flex lg:flex-row flex-col lg:gap-16 gap-5 justify-between">
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="auction">Type of Auction</label>
                            <select name="auction" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required>
                                <option value="forward" <%= typeof auction !== 'undefined' && auction === 'forward' ? 'selected' : '' %>>Forward Auction</option>
                                <option value="reverse" <%= typeof auction !== 'undefined' && auction === 'reverse' ? 'selected' : '' %>>Reverse Auction</option>
                            </select>                          
                        </div> 
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="price">Base Price</label>
                            <input type="number" name="price" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required value="<%= typeof price !== 'undefined' ? price : '' %>">
                        </div>
                    </div>  
                    <div class="flex lg:flex-row flex-col lg:gap-16 gap-5 justify-between">
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="date">Auction Date</label>
                            <input type="date" name="date" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required value="<%= typeof date !== 'undefined' ? date : '' %>">
                        </div>  
                        <div class="flex lg:flex-row flex-col justify-between items-center lg:gap-4 gap-1 lg:w-[620px] w-[300px]">
                            <label for="time">Auction Time</label>
                            <input type="time" name="time" class="rounded-md text-black p-2 text-sm lg:w-[400px] w-[300px]" required value="<%= typeof time !== 'undefined' ? time : '' %>">
                        </div>
                    </div>
                </div>
                <div class="flex lg:flex-row flex-col justify-center items-center lg:gap-14 gap-2 lg:w-[620px] w-[300px]">
                    <label for="image">Upload Images</label>
                    <input type="file" name="image" id="image" accept="image/*" class="lg:w-[400px] w-[220px]">
                </div> 
                <button type="submit" class="bg-gradient-to-b from-lime-700 to-lime-900 lg:text-2xl text-lg lg:py-2 lg:px-4 py-1 px-3 rounded-full transform transition-transform duration-150 ease-in-out active:scale-95">Post</button>
            </form>
        </div>
    </div>

    <div id="titleModal" class="modal">
        <div class="modal-content relative flex flex-col lg:gap-4 gap-2 lg:w-[50%] w-[300px] rounded-md">
            <span class="close absolute top-1 right-14" onclick="closeModal('titleModal')">&times;</span>
            <span class="edit absolute top-3 right-6" onclick="editModal('titleModal', 'title')">&#x2713;</span>
            <label for="title" class="text-xl">Job Title</label>
            <textarea id="titleTextarea" class="rounded-md text-black p-2 text-sm w-full h-full" required><%= typeof title !== 'undefined' ? title : '' %></textarea>
        </div>
    </div>
    <div id="descriptionModal" class="modal">
        <div class="modal-content relative flex flex-col lg:gap-4 gap-2 lg:w-[50%] w-[300px] rounded-md">
            <span class="close absolute top-1 right-14" onclick="closeModal('descriptionModal')">&times;</span>
            <span class="edit absolute top-3 right-6" onclick="editModal('descriptionModal', 'description')">&#10003;</span>
            <label for="description" class="text-xl">Job Description</label>
            <textarea id="descriptionTextarea" class="rounded-md text-black p-2 text-sm w-full h-full" required><%= typeof description !== 'undefined' ? description : '' %></textarea>
        </div>
    </div>

    <script>
        function openModal(modalId) {
            document.getElementById(modalId).style.display = "flex";
        }
        function closeModal(modalId) {
            document.getElementById(modalId).style.display = "none";
        }
        window.onclick = function(event) {
            const modals = document.querySelectorAll('.modal');
            modals.forEach(modal => {
                if (event.target == modal) {
                    modal.style.display = "none";
                }
            });
        }

        function validateContactNumber() {
            const contactInput = document.getElementById('contact');
            const contactValue = contactInput.value;
            const isValid = /^\d{10}$/.test(contactValue);    
            if (!isValid) {
                alert('Please enter a valid 10-digit contact number.');
                return false;
            }
            return true;
        }

        function editModal(modalId, inputId) {
            const modal = document.getElementById(modalId);
            const textarea = modal.querySelector('textarea');
            const input = document.getElementById(inputId);
            input.value = textarea.value;
            closeModal(modalId);
        }
    </script>
</body>
</html>

```