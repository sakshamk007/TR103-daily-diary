# Day 11

## Created auth/signin routes

```
router.get('/signin', (req, res) => {
    res.render('web/layouts/auth', { page: 'signin' });
});
```

```
router.post('/signin', async (req, res) => {
    const { email, password } = req.body;
    const user_agent = req.headers['user-agent'];
    try {
        // const [rows] = await pool.query('SELECT * FROM users WHERE email = ?', [email]);
        const rows = await User.read(email);        
        if (rows.length === 0) {
            return res.status(400).render('web/layouts/auth', { page: 'error', status: 400, message: 'User not found' });
        }
        const user = rows[0];
        const match = await bcrypt.compare(password, user.password);
        if (!match) {
            return res.status(400).render('web/layouts/auth', { page: 'error', status: 400, message: 'Invalid password' });
        }
        const session_id = crypto.randomBytes(16).toString('hex');
        const expiry = new Date(Date.now() + 30 * 60 * 1000);
        // await pool.query('INSERT INTO sessions (session_id, user_id, user_agent, expiry, last_activity) VALUES (?, ?, ?, ?, NOW())', [session_id, user.user_id, user_agent, expiry]);
        await Session.add(session_id, user.user_id, user_agent, expiry);
        res.cookie('session_id', session_id, { httpOnly: true, maxAge: 24 * 60 * 60 * 1000 });
        res.cookie('user_id', user.user_id);        
        const rows1 = await Profile.findByEmail(email)
        if (rows1.length === 0) {
            const user_id = user.user_id
            res.render('web/layouts/auth', { page: 'profile', user_id, email });
        }
        else{
            res.redirect('/welcome');
        }
    } catch (error) {
        console.error(error);
        res.status(500).render('web/layouts/auth', { page: 'error', status: 500, message: 'Internal server error' });
    }
});
```