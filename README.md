document.getElementById("walletBalance").innerText = balance.toFixed(2);
    localStorage.setItem("balance", balance);
}

// Ads Handling
async function handleAd(type) {
    document.getElementById("status").innerText = "এড লোড হচ্ছে, অপেক্ষা করুন...";
    
    if (typeof show_12341336 === "function") {
        try {
            await show_12341336(type === 'pop' ? 'pop' : '');
            balance += 0.15;
            updateBalanceDisplay();
            toast("অভিনন্দন! ০.১৫৳ যোগ হয়েছে।");
            document.getElementById("status").innerText = "টাস্ক সম্পন্ন হয়েছে";
        } catch (e) {
            toast("এড লোড করতে ব্যর্থ। আবার চেষ্টা করুন।");
            document.getElementById("status").innerText = "সার্ভার বিজি";
        }
    } else {
        toast("Ad SDK লোড হচ্ছে... ৫ সেকেন্ড পর চেষ্টা করুন");
    }
}

function startAds() {
    if (typeof show_12341336 === "function") {
        show_12341336({ type: 'inApp', inAppSettings: { frequency: 2, interval: 30 } });
        toast("অটো ভিডিও এড চালু হয়েছে");
    } else {
        toast("SDK লোড হয়নি");
    }
}

// Withdraw Logic
function withdrawNow() {
    let amount = parseFloat(document.getElementById("withdrawAmount").value);
    let method = document.getElementById("method").value;
    let number = document.getElementById("number").value;

    if (isNaN(amount) || amount < 50) {
        toast("মিনিমাম ৫০ টাকা থাকতে হবে");
        return;
    }
    if (amount > balance) {
        toast("আপনার ব্যালেন্স কম");
        return;
    }
    if (number.length < 11) {
        toast("সঠিক ১১ ডিজিটের নাম্বার দিন");
        return;
    }

    // Process Withdraw
    balance -= amount;
    updateBalanceDisplay();

    let history = JSON.parse(localStorage.getItem("withdrawHistory") || "[]");
    history.unshift({ amount, method, number, status: "Pending", date: new Date().toLocaleDateString() });
    localStorage.setItem("withdrawHistory", JSON.stringify(history));

    toast("রিকুয়েস্ট সফল! ২৪ ঘণ্টার মধ্যে পেমেন্ট পাবেন।");
    loadWithdrawHistory();
}

function toggleHistory() {
    let box = document.getElementById("historyCard");
    box.style.display = box.style.display === "none" ? "block" : "none";
    if(box.style.display === "block") loadWithdrawHistory();
}

function loadWithdrawHistory() {
    let history = JSON.parse(localStorage.getItem("withdrawHistory") || "[]");
    let container = document.getElementById("withdrawHistory");
    if(history.length === 0) {
        container.innerHTML = "<p style='text-align:center; color:gray;'>কোনো রেকর্ড নেই</p>";
        return;
    }
    container.innerHTML = history.map(item => `
        <div class="task">
            <div><b>${item.amount}৳ (${item.method})</b><br><small>${item.date}</small></div>
            <span style="color:${item.status==='Pending'?'#ff9800':'#22c55e'}; font-weight:bold;">${item.status}</span>
        </div>
    `).join('');
}

// Referral
let ref = localStorage.getItem("refCode") || Math.random().toString(36).substring(2, 8).toUpperCase();
localStorage.setItem("refCode", ref);
document.getElementById("refCode").innerText = ref;

function shareReferral() {
    let link = "https://t.me/YOUR_BOT_USERNAME?start=" + ref; 
    navigator.clipboard.writeText(link);
    toast("লিংক কপি হয়েছে! বন্ধুদের পাঠান।");
}

function logoutUser() {
    if(confirm("আপনি কি সব ডেটা মুছে ফেলতে চান?")) {
        localStorage.clear();
        location.reload();
    }
}

// Initial Ranking Load
function loadRanking() {
    const names = ["Rahim", "Karim", "Emran", "Jahid", "Siam", "Monir", "Tanvir", "Sajib"];
    let html = names.map((name, i) => `
        <div class="task">
            <div><b>#${i+1} ${name}</b><br><small>${(Math.random()*500 + 100).toFixed(2)}৳</small></div>
            <i class="fa-solid fa-trophy" style="color:#ff9800;"></i>
        </div>
    `).join('');
    document.getElementById("rankingBox").innerHTML = html;
}
loadRanking();
</script>
</body>
</html>
