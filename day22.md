# Day 22

## Created /bid route

```
router.post('/bid', authenticate, async (req,res)=>{
    const { auction, bid_id, user_id, price } = req.body;
    if (auction === 'forward'){
        const rows = await Contract.browseDesc(bid_id);
        const maxBid = await Contract.getMaxBid(bid_id);
        let currentBid = maxBid.max_value
        if (currentBid === null){
            currentBid = price;
        }
        res.render('web/pages/bid', {layout: "web/pages/bid", auction, bid_id, user_id, rows, currentBid, price})
    }
    else if (auction === 'reverse'){
        const rows = await Contract.browseAsc(bid_id);
        const minBid = await Contract.getMinBid(bid_id);
        let currentBid = minBid.min_value
        if (currentBid === null){
            currentBid = price;
        }
        res.render('web/pages/bid', {layout: "web/pages/bid", auction, bid_id, user_id, rows, currentBid, price})
    }
})

router.post('/submit-bid', authenticate, async (req,res)=>{
    const { user_id, bidValue, auction, bid_id, price } = req.body;
    const value = Number(bidValue);
    const email = await User.getEmailId(user_id);
    const username = await Profile.getUsername(user_id);
    if (auction === 'forward'){
        const maxBid = await Contract.getMaxBid(bid_id);
        let currentBid = maxBid.max_value
        if (currentBid === null){
            currentBid = price;
        }
        if (value < currentBid || value === currentBid){
            return res.status(400).json({ error: 'Bid Value should be greater than Current Bid' });
        }
        else {
            await Contract.add(user_id, bid_id, value, email.email, auction, username.username);
            return res.status(200).json({ message: 'Bid submitted successfully' });
        }
    }
    else if (auction === 'reverse'){
        const minBid = await Contract.getMinBid(bid_id);
        let currentBid = minBid.min_value
        if (currentBid === null){
            currentBid = price;
        }
        if (value > currentBid || value === currentBid){
            return res.status(400).json({ error: 'Bid Value should be less than Current Bid' });
        }
        else if (value < 1 || value === 0){
            return res.status(400).json({ error: 'Bid Value should not be less than or equal to 0' });
        }
        else {
            await Contract.add(user_id, bid_id, value, email.email, auction, username.username);
            return res.status(200).json({ message: 'Bid submitted successfully' });
        }
    }
})

const updateTimer = async () => {
    const bids = await Bid.getAll();
    const now = new Date().toLocaleTimeString('en-GB', { timeZone: 'Asia/Kolkata' }, { hour12: false }).split(' ')[0]
    bids.forEach(async (bid) => {
        const bid_id = bid.bid_id;
        let timer = bid.timer;
        const time = bid.time;
        if (timer > 0 && time <= now){
            timer--; 
            // await pool.query('UPDATE bids SET timer = ? WHERE bid_id = ?', [timer, bid_id])
            await Bid.updateTimer(timer, bid_id)
        }
    });
}
setInterval(updateTimer, 1000);

router.get('/bids-and-timer', authenticate, async (req, res) => {
    const { bid_id, auction, price } = req.query;
    // let [rows] = await pool.query('SELECT timer FROM bids WHERE bid_id = ?', [bid_id])
    let rows = await Bid.getTimer(bid_id)
    let timer = rows[0].timer
    if (timer > 0) {
        if (auction === 'forward') {
            const rows = await Contract.browseDesc(bid_id);
            const maxBid = await Contract.getMaxBid(bid_id);
            let currentBid = maxBid.max_value
            if (currentBid === null){
                currentBid = price;
            }
            return res.json({ rows, currentBid, timer});
        } else if (auction === 'reverse') {
            const rows = await Contract.browseAsc(bid_id);
            const minBid = await Contract.getMinBid(bid_id);
            let currentBid = minBid.min_value
            if (currentBid === null){
                currentBid = price;
            }
            return res.json({ rows, currentBid, timer});
        }
    }
    else {
        return res.status(400).json({ error: 'Bidding ended' });
    }
});
```