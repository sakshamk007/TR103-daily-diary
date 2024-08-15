# Day 30

## Created models - image.js, contract.js, wishlist.js, status.js

### image.js
```
const pool = require('@configs/database');

const Image = {
    add: async (bid_id, image) => {
        const [result] = await pool.execute('INSERT INTO images (image_id, image_path) VALUES (?, ?)', [bid_id, image]);
        return result;
    },
    findByImageId: async (bid_id) => {
        const [result] = await pool.execute('SELECT image_path FROM images WHERE image_id = ?', [bid_id]);
        return result;
    },
    delete: async (bid_id) => {
        const [result] = await pool.execute('DELETE FROM images WHERE image_id = ?', [bid_id]);
        return result;
    }
};

module.exports = Image;
```

### contract.js
```
const pool = require('@configs/database');

const Contract = {
    browseDesc: async (id) => {
        const [rows] = await pool.execute('SELECT * FROM contracts WHERE bid_id = ? ORDER BY value DESC LIMIT 10', [id]);
        return rows;
    },
    browseAsc: async (id) => {
        const [rows] = await pool.execute('SELECT * FROM contracts WHERE bid_id = ? ORDER BY value ASC LIMIT 10', [id]);
        return rows;
    },
    add: async (user_id, bid_id, bidValue, email, auction, username) => {
        const [result] = await pool.execute('INSERT INTO contracts (user_id, bid_id, value, email, auction, username, created_at) VALUES (?, ?, ?, ?, ?, ?, NOW())', [user_id, bid_id, bidValue, email, auction, username]);
        return result;
    },
    getMaxBid: async (id) => {
        const [rows] = await pool.execute('SELECT MAX(value) AS max_value FROM contracts WHERE bid_id = ?', [id]);
        return rows[0];
    },    
    getMinBid: async (id) => {
        const [rows] = await pool.execute('SELECT MIN(value) AS min_value FROM contracts WHERE bid_id = ?', [id]);
        return rows[0];
    },    
    findByUserId: async (userId) => {
        const [rows] = await pool.execute('SELECT * FROM bids WHERE bid_id IN (SELECT bid_id FROM contracts WHERE user_id = ?) ORDER BY DATE(date) ASC, TIME(time) ASC', [userId]);
        return rows;
    },
    getHighestBidder: async (bid_id) => {
        const [rows] = await pool.execute('SELECT * FROM contracts WHERE bid_id = ? ORDER BY value DESC LIMIT 1', [bid_id]);
        return rows[0];
    },
    getLowestBidder: async (bid_id) => {
        const [rows] = await pool.execute('SELECT * FROM contracts WHERE bid_id = ? ORDER BY value ASC LIMIT 1', [bid_id]);
        return rows[0];
    },
    delete: async (bid_id) => {
        const [result] = await pool.execute('DELETE FROM contracts WHERE bid_id = ?', [bid_id]);
        return result;
    }
};

module.exports = Contract;
```

### wishlist.js
```
const pool = require('@configs/database');

const Wishlist = {
    read: async (bid_id, user_id) => {
        const [rows] = await pool.execute('SELECT * FROM wishlist WHERE wishlist_id = ? AND user_id = ?', [bid_id, user_id]);
        return rows;
    },
    add: async (bid_id, user_id, title) => {
        const [result] = await pool.execute('INSERT INTO wishlist (wishlist_id, user_id, title) VALUES (?, ?, ?)', [bid_id, user_id, title]);
        return result;
    },
    findByUserId: async (user_id) => {
        const [rows] = await pool.execute('SELECT * FROM wishlist WHERE user_id = ? AND wishlist_id IN (SELECT bid_id FROM bids WHERE timer > 0 AND DATE(date) > DATE(NOW()) OR (DATE(date) = DATE(NOW()) AND TIME(time) >= TIME(NOW())));', [user_id]);
        return rows;
    },
    delete: async (wishlist_id, user_id) => {
        const [result] = await pool.execute('DELETE FROM wishlist WHERE wishlist_id = ? AND user_id = ?', [wishlist_id, user_id]);
        return result;
    },
    deleteForAll: async (wishlist_id) => {
        const [result] = await pool.execute('DELETE FROM wishlist WHERE wishlist_id = ?', [wishlist_id]);
        return result;
    },
};

module.exports = Wishlist;
```

### status.js
```
const pool = require('@configs/database');

const Status = {
    add: async (bid_id, username, name, contact_no, email, years_of_experience, value, response) => {
        const [result] = await pool.execute('INSERT INTO status (bid_id, username, name, contact_no, email, years_of_experience, value, response) VALUES (?, ?, ?, ?, ?, ?, ?, ?)', [bid_id, username, name, contact_no, email, years_of_experience, value, response]);
        return result;
    },
    getResponse: async (bid_id) => {
        const [result] = await pool.execute('SELECT response FROM status WHERE bid_id = ?', [bid_id])
        return result[0];
    },
    delete: async (bid_id) => {
        const [result] = await pool.execute('DELETE FROM status WHERE bid_id = ?', [bid_id]);
        return result;
    }
};

module.exports = Status;
```