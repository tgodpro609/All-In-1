import React, { useEffect, useState } from 'react';
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(
  'https://acqpxisxxvkslrnlquos.supabase.co',
  'sb_publishable_2bKqTv5vwKN4DFMXmGKfig_t_hAqHTi'
);

export default function App() {
  const [loggedIn, setLoggedIn] = useState(false);
  const [isCreating, setIsCreating] = useState(false);
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [email, setEmail] = useState('');
  const [verificationCode, setVerificationCode] = useState('');
  const [enteredCode, setEnteredCode] = useState('');
  const [needsVerification, setNeedsVerification] = useState(false);
  const [profilePic, setProfilePic] = useState('https://tr.rbxcdn.com/180DAY-1f2c6c44f3e7d741a2a3e3f0f5caa123/150/150/AvatarHeadshot/Png/noFilter');
  const [selectedVote, setSelectedVote] = useState('');
  const [onlineUsers, setOnlineUsers] = useState(1);
  const [chatMessage, setChatMessage] = useState('');
  const [chat, setChat] = useState<string[]>([]);

  const candidates = ['Rin', 'Isagi', 'Nagi', 'Bachira'];

  const [votes, setVotes] = useState<{ [key: string]: number }>({
    Rin: 0,
    Isagi: 0,
    Nagi: 0,
    Bachira: 0,
  });

  useEffect(() => {
    const randomUsers = Math.floor(Math.random() * 50) + 1;
    setOnlineUsers(randomUsers);

    loadMessages();

    const channel = supabase
      .channel('global-chat')
      .on(
        'postgres_changes',
        {
          event: 'INSERT',
          schema: 'public',
          table: 'messages',
        },
        (payload) => {
          const newMessage = `${payload.new.username}: ${payload.new.text}`;

          setChat((prev) => {
            if (prev.includes(newMessage)) return prev;
            return [...prev, newMessage];
          });
        }
      )
      .subscribe();

    return () => {
      supabase.removeChannel(channel);
    };
  }, []);

  const createAccount = () => {
    if (!username || !password) {
      alert('Enter username and password');
      return;
    }

    localStorage.setItem(
      'account',
      JSON.stringify({
        username,
        password,
        email,
        profilePic,
      })
    );

    if (email) {
      const code = Math.floor(100000 + Math.random() * 900000).toString();
      setVerificationCode(code);
      setNeedsVerification(true);
    } else {
      setLoggedIn(true);
    }
  };

  const verifyCode = () => {
    if (enteredCode === verificationCode) {
      setLoggedIn(true);
      setNeedsVerification(false);
    } else {
      alert('Wrong code');
    }
  };

  const login = () => {
    const stored = localStorage.getItem('account');

    if (!stored) {
      alert('Create an account first');
      return;
    }

    const account = JSON.parse(stored);

    if (account.username === username && account.password === password) {
      setProfilePic(account.profilePic);
      setLoggedIn(true);
    } else {
      alert('Wrong username or password');
    }
  };

  const vote = (name: string) => {
    const updatedVotes = { ...votes };

    if (selectedVote) {
      updatedVotes[selectedVote] -= 1;
    }

    updatedVotes[name] += 1;

    setVotes(updatedVotes);
    setSelectedVote(name);
  };

  const leaderboard = Object.entries(votes).sort((a, b) => b[1] - a[1]);

  const loadMessages = async () => {
    const { data } = await supabase
      .from('messages')
      .select('*')
      .order('created_at', { ascending: true });

    if (data) {
      setChat(data.map((msg) => `${msg.username}: ${msg.text}`));
    }
  };

  const sendMessage = async () => {
    if (!chatMessage.trim()) return;

    await supabase.from('messages').insert([
      {
        username,
        text: chatMessage,
      },
    ]);

    setChatMessage('');
  };

  if (needsVerification) {
    return (
      <div className="min-h-screen bg-black text-white flex flex-col items-center justify-center p-6">
        <img
          src="https://files.catbox.moe/8x8l8l.png"
          className="w-40 mb-6 rounded-2xl"
        />

        <div className="bg-yellow-400 text-black text-4xl font-bold px-8 py-4 rounded-2xl mb-6 shadow-lg">
          {verificationCode}
        </div>

        <h1 className="text-3xl font-bold mb-4">Verify Account</h1>

        <input
          placeholder="Enter 6-digit code"
          value={enteredCode}
          onChange={(e) => setEnteredCode(e.target.value)}
          className="p-3 rounded-xl text-black w-72 mb-4"
        />

        <button
          onClick={verifyCode}
          className="bg-blue-500 hover:bg-blue-600 px-8 py-3 rounded-2xl font-bold"
        >
          Verify
        </button>
      </div>
    );
  }

  if (!loggedIn) {
    return (
      <div className="min-h-screen bg-black text-white flex items-center justify-center p-6">
        <div className="bg-zinc-900 p-8 rounded-3xl w-full max-w-md shadow-2xl border border-blue-500">
          <img
            src="https://files.catbox.moe/8x8l8l.png"
            className="w-36 mx-auto mb-6 rounded-2xl"
          />

          <h1 className="text-4xl font-bold text-center mb-6">
            Escape Tsunami Vote
          </h1>

          <input
            placeholder="Username"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
            className="w-full p-3 rounded-xl text-black mb-4"
          />

          <input
            type="password"
            placeholder="Password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            className="w-full p-3 rounded-xl text-black mb-4"
          />

          <input
            placeholder="Optional Gmail"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
            className="w-full p-3 rounded-xl text-black mb-4"
          />

          <input
            placeholder="Profile Picture URL"
            value={profilePic}
            onChange={(e) => setProfilePic(e.target.value)}
            className="w-full p-3 rounded-xl text-black mb-6"
          />

          <button
            onClick={isCreating ? createAccount : login}
            className="w-full bg-blue-500 hover:bg-blue-600 p-3 rounded-2xl font-bold text-lg"
          >
            {isCreating ? 'Create Account' : 'Login'}
          </button>

          <button
            onClick={() => setIsCreating(!isCreating)}
            className="w-full mt-4 text-blue-400"
          >
            {isCreating
              ? 'Already have an account? Login'
              : 'Create Account'}
          </button>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-b from-black to-blue-950 text-white p-6">
      <div className="flex items-center justify-between mb-8">
        <div className="flex items-center gap-4">
          <img
            src={profilePic}
            className="w-16 h-16 rounded-full border-4 border-blue-400"
          />

          <div>
            <h2 className="text-2xl font-bold">{username}</h2>
            <p className="text-green-400">🟢 {onlineUsers} Online</p>
          </div>
        </div>

        <img
          src="https://files.catbox.moe/8x8l8l.png"
          className="w-24 rounded-xl"
        />
      </div>

      <div className="bg-zinc-900 rounded-3xl p-6 shadow-2xl border border-blue-500 mb-8">
        <h1 className="text-4xl font-extrabold text-center mb-8">
          WHO TO ADD NEXT IN ESCAPE TSUNAMI FOR BLUE LOCK?
        </h1>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
          {candidates.map((name) => (
            <button
              key={name}
              onClick={() => vote(name)}
              className={`p-6 rounded-3xl text-2xl font-bold transition-all duration-300 border-2 ${
                selectedVote === name
                  ? 'bg-blue-500 border-white scale-105'
                  : 'bg-zinc-800 border-blue-400 hover:bg-blue-700'
              }`}
            >
              {name}
            </button>
          ))}
        </div>
      </div>

      <div className="bg-zinc-900 rounded-3xl p-6 border border-yellow-400 shadow-xl mb-8">
        <h2 className="text-3xl font-bold mb-6">🏆 Leaderboard</h2>

        <div className="space-y-4">
          {leaderboard.map(([name, count], index) => (
            <div
              key={name}
              className="flex justify-between bg-black/40 p-4 rounded-2xl"
            >
              <span className="font-bold text-xl">
                #{index + 1} {name}
              </span>

              <span className="text-yellow-300 text-xl font-bold">
                {count} Votes
              </span>
            </div>
          ))}
        </div>
      </div>

      <div className="bg-zinc-900 rounded-3xl p-6 border border-green-400 shadow-xl">
        <h2 className="text-3xl font-bold mb-4">💬 Global Chat</h2>

        <div className="bg-black/40 rounded-2xl p-4 h-48 overflow-y-auto mb-4">
          {chat.length === 0 ? (
            <p className="text-gray-400">No messages yet...</p>
          ) : (
            chat.map((msg, index) => (
              <div key={index} className="mb-2">
                {msg}
              </div>
            ))
          )}
        </div>

        <div className="flex gap-4">
          <input
            value={chatMessage}
            onChange={(e) => setChatMessage(e.target.value)}
            placeholder="Type message..."
            className="flex-1 p-3 rounded-xl text-black"
          />

          <button
            onClick={sendMessage}
            className="bg-green-500 hover:bg-green-600 px-6 rounded-2xl font-bold"
          >
            Send
          </button>
        </div>
      </div>
    </div>
  );
}
