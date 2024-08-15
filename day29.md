# Day 29

## Created models - user.js, session.js, profile.js, bid.js

### user.js
```
const pool = require('@configs/database');

const User = {
    read: async (email) => {
        const [rows] = await pool.execute('SELECT * FROM users WHERE email = ?', [email]);
        return rows;
    },
    add: async (email, password, id) => {
      const [result] = await pool.execute('INSERT INTO users (email, password, user_id) VALUES (?, ?, ?)', [email, password, id]);
      return result;
    },
    getEmailId: async (id) => {
        const [rows] = await pool.execute('SELECT email FROM users WHERE user_id = ?', [id]);
        return rows[0];
    }
};

module.exports = User;
```

### session.js 
```
const pool = require('@configs/database');

const Session = {
    read: async (id) => {
        const [rows] = await pool.execute('SELECT * FROM sessions WHERE session_id = ? AND expiry > NOW()', [id]);
        return rows;
    },
    edit: async (id) => {
        const [result] = await pool.execute('UPDATE sessions SET expiry = DATE_ADD(NOW(), INTERVAL 30 MINUTE), last_activity = NOW() WHERE session_id = ?', [id]);
        return result;
    },
    add: async (session_id, user_id, user_agent, expiry) => {
      const [result] = await pool.execute('INSERT INTO sessions (session_id, user_id, user_agent, expiry, last_activity) VALUES (?, ?, ?, ?, NOW())', [session_id, user_id, user_agent, expiry]);
      return result;
    },
    getSessionId: async () => {
        const [rows] = await pool.execute('SELECT session_id FROM sessions WHERE last_activity < DATE_SUB(NOW(), INTERVAL 30 MINUTE) AND terminated_at IS NULL');
        return rows;
    },
    updateTerminatedAt: async (id) => {
        const [result] = await pool.execute('UPDATE sessions SET terminated_at = NOW() WHERE session_id = ?', [id]);
        return result;
    }
};

module.exports = Session;
```

### profile.js
```
const pool = require('@configs/database');

const Profile = {
    browse: async (username, email) => {
        const [rows] = await pool.execute('SELECT * FROM profile WHERE username = ? OR email = ?', [username, email]);
        return rows;
    },
    add: async (user_id, email, contact, username, first, last, experience) => {
        const [result] = await pool.execute('INSERT INTO profile (user_id, email, contact_no, username, first_name, last_name, years_of_experience) VALUES (?, ?, ?, ?, ?, ?, ?)', [user_id, email, contact, username, first, last, experience]);
        return result;
    },
    findByEmail: async (email) => {
        const [rows] = await pool.execute('SELECT * FROM profile WHERE email = ?', [email]);
        return rows;
    },
    findByUserId: async (user_id) => {
        const [rows] = await pool.execute('SELECT * FROM profile WHERE user_id = ?', [user_id]);
        return rows[0];
    },
    getUsername: async (user_id) => {
        const [rows] = await pool.execute('SELECT username FROM profile WHERE user_id = ?', [user_id]);
        return rows[0];
    },
    delete: async (user_id) => {
        const [result] = await pool.execute('DELETE FROM profile WHERE user_id = ?', [user_id]);
        return result;
    },
}

module.exports = Profile;
```

### bid.js
```
const pool = require('@configs/database');

const Bid = {
    browse: async () => {
        const [result] = await pool.execute('SELECT * FROM bids WHERE timer > 0 AND DATE(date) > DATE(NOW()) OR (DATE(date) = DATE(NOW()) AND TIME(time) >= TIME(NOW())) ORDER BY DATE(date) ASC, TIME(time) ASC');
        return result;
    },
    add: async (bid_id, user_id, name, email, title, auction, date, type, contact, description, price, time) => {
        const [result] = await pool.execute('INSERT INTO bids (bid_id, user_id, name, email, title, auction, date, type, contact, description, price, time) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)', [bid_id, user_id, name, email, title, auction, date, type, contact, description, price, time]);
        return result;
    },
    exists: async (user_id, title, name, description, auction) => {
        const [rows] = await pool.execute('SELECT * FROM bids WHERE user_id = ? AND title = ? AND name = ? AND description = ? AND auction = ?', [user_id, title, name, description, auction]);
        return rows.length > 0;
    },
    findByUserId: async (userId) => {
        const [rows] = await pool.execute('SELECT * FROM bids WHERE user_id = ? ORDER BY DATE(date) ASC, TIME(time) ASC', [userId]);
        return rows;
    },
    findByBidId: async (Bid_id) => {
        const [rows] = await pool.execute('SELECT * FROM bids WHERE Bid_id = ?', [Bid_id]);
        return rows;
    },
    getTimer: async (bid_id) => {
        let [rows] = await pool.execute('SELECT timer FROM bids WHERE bid_id = ?', [bid_id]);
        return rows;
    },
    updateTimer: async (timer, bid_id) => {
        const [rows] = await pool.execute('UPDATE bids SET timer = ? WHERE bid_id = ?', [timer, bid_id]);
        return rows;
    },
    getUserId: async (userId) => {
        const [rows] = await pool.execute('SELECT user_id FROM bids WHERE bid_id = ?', [userId]);
        return rows[0];
    },
    getAll: async () => {
        const [rows] = await pool.execute('SELECT * FROM bids');
        return rows;
    },
    delete: async (bid_id, user_id) => {
        const [result] = await pool.execute('DELETE FROM bids WHERE bid_id = ? AND user_id = ?', [bid_id, user_id]);
        return result;
    }
};

module.exports = Bid;
```