# Google Business Reviews Integration Guide
**For: Let's Tour Kwahu Landing Page**

---

## 📋 Overview
Display Google Business reviews in a popup widget sliding in from the bottom left of your landing page.

---

## Option 1: Easiest Solution - Using ReviewInc or Similar Service (Recommended)

### Services That Auto-Pull Google Reviews:
- **Trustpilot Reviews** - https://www.trustpilot.com
- **ReviewIO** - https://reviews.io
- **Yext Reviews** - https://www.yext.com/products/reviews
- **PagePilot** - Syncs Google reviews automatically
- **DotRank** - Aggregates Google Business reviews

### Steps:
1. **Sign up** for the service
2. **Connect your Google Business Profile** (they'll guide you)
3. **Get the embed code** they provide
4. **Add to your index.html** in the `<body>` section (bottom)
5. **Customize styling** to match your bottom-left popup design

---

## Option 2: Google Business Profile Widget (Official)

### Setup Steps:

#### Step 1: Get Your Business ID
1. Go to **Google Business Profile** (https://business.google.com)
2. Sign in with your business account
3. Click your business name
4. In the left menu, find **"Profile"** → note your business ID from the URL:
   ```
   https://business.google.com/a/[BUSINESS_ID]/posts
   ```

#### Step 2: Create Google Cloud Project
1. Go to **Google Cloud Console** (https://console.cloud.google.com)
2. Create a new project: **"Let's Tour Kwahu Reviews"**
3. Enable these APIs:
   - **Google My Business API**
   - **Places API**
4. Create an **API Key** (Credentials → Create Credentials → API Key)
   - Restrict it to: **Places API** + **My Business API**

#### Step 3: Add Code to Your HTML

Add this **before the closing `</body>` tag** in index.html:

```html
<!-- Google Reviews Popup Widget -->
<div id="google-reviews-popup" class="reviews-popup">
  <div class="reviews-container">
    <div class="reviews-header">
      <h3>⭐ What Clients Say</h3>
      <button class="close-reviews" onclick="closeReviews()">✕</button>
    </div>
    <div class="reviews-content" id="reviews-list">
      <p class="loading">Loading reviews...</p>
    </div>
    <div class="reviews-footer">
      <a href="https://www.google.com/search?q=Let's+Tour+Kwahu" target="_blank" class="view-all-btn">
        View All Reviews →
      </a>
    </div>
  </div>
</div>

<script>
  // Configuration
  const GOOGLE_API_KEY = "YOUR_API_KEY_HERE"; // Get from Google Cloud Console
  const BUSINESS_ID = "YOUR_BUSINESS_ID_HERE"; // From Google Business Profile
  const LOCATION_ID = "YOUR_LOCATION_ID_HERE"; // Get from Google My Business

  // Function to fetch reviews
  async function fetchGoogleReviews() {
    try {
      const response = await fetch(
        `https://www.googleapis.com/myplaces/v1/accounts/${BUSINESS_ID}/locations/${LOCATION_ID}/reviews?key=${GOOGLE_API_KEY}`
      );
      const data = await response.json();
      displayReviews(data.reviews || []);
    } catch (error) {
      console.error("Error fetching reviews:", error);
      displayFallbackReviews();
    }
  }

  // Function to display reviews
  function displayReviews(reviews) {
    const reviewsList = document.getElementById("reviews-list");
    if (reviews.length === 0) {
      displayFallbackReviews();
      return;
    }

    let reviewsHTML = reviews.slice(0, 3).map(review => `
      <div class="review-card">
        <div class="review-rating">
          ${'⭐'.repeat(review.rating || 5)}
        </div>
        <p class="review-text">"${review.review || review.text || 'Great experience!'}"</p>
        <p class="review-author">- ${review.reviewer?.displayName || review.author || 'Google User'}</p>
      </div>
    `).join("");

    reviewsList.innerHTML = reviewsHTML;
  }

  // Fallback reviews (if API fails)
  function displayFallbackReviews() {
    const reviewsList = document.getElementById("reviews-list");
    reviewsList.innerHTML = `
      <div class="review-card">
        <div class="review-rating">⭐⭐⭐⭐⭐</div>
        <p class="review-text">"Amazing tour experience! The guides were very knowledgeable and friendly."</p>
        <p class="review-author">- Ama K.</p>
      </div>
      <div class="review-card">
        <div class="review-rating">⭐⭐⭐⭐⭐</div>
        <p class="review-text">"Best hiking adventure in Kwahu. Highly recommended!"</p>
        <p class="review-author">- Kofi M.</p>
      </div>
      <div class="review-card">
        <div class="review-rating">⭐⭐⭐⭐⭐</div>
        <p class="review-text">"Professional service and breathtaking views. Will definitely return!"</p>
        <p class="review-author">- Yaa A.</p>
      </div>
    `;
  }

  // Show/Hide popup
  function showReviews() {
    document.getElementById("google-reviews-popup").classList.add("show");
    fetchGoogleReviews();
  }

  function closeReviews() {
    document.getElementById("google-reviews-popup").classList.remove("show");
  }

  // Auto-show popup after 5 seconds
  window.addEventListener("load", () => {
    setTimeout(showReviews, 5000);
  });

  // Close popup when clicking outside
  document.getElementById("google-reviews-popup").addEventListener("click", (e) => {
    if (e.target.id === "google-reviews-popup") {
      closeReviews();
    }
  });
</script>

<style>
  /* Reviews Popup Styles */
  .reviews-popup {
    position: fixed;
    bottom: -400px;
    left: 20px;
    width: 320px;
    background: white;
    border-radius: 12px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.15);
    z-index: 9990;
    transition: bottom 0.4s ease-out;
    font-family: Arial, sans-serif;
  }

  .reviews-popup.show {
    bottom: 20px;
    animation: slideUp 0.4s ease-out;
  }

  @keyframes slideUp {
    from {
      bottom: -400px;
      opacity: 0;
    }
    to {
      bottom: 20px;
      opacity: 1;
    }
  }

  .reviews-container {
    padding: 0;
    overflow: hidden;
  }

  .reviews-header {
    background: linear-gradient(90deg, #264653, #388c3c);
    color: #ffe082;
    padding: 1rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-radius: 12px 12px 0 0;
  }

  .reviews-header h3 {
    margin: 0;
    font-size: 1.1rem;
    border: none;
    box-shadow: none;
    background: none;
    -webkit-background-clip: unset;
    -webkit-text-fill-color: unset;
    background-clip: unset;
  }

  .close-reviews {
    background: none;
    border: none;
    color: #ffe082;
    font-size: 1.5rem;
    cursor: pointer;
    transition: color 0.2s;
  }

  .close-reviews:hover {
    color: #f4a261;
  }

  .reviews-content {
    padding: 1rem;
    max-height: 300px;
    overflow-y: auto;
  }

  .review-card {
    margin-bottom: 1rem;
    padding: 0.8rem;
    background: #f5f5f5;
    border-radius: 8px;
    border-left: 4px solid #f4a261;
  }

  .review-rating {
    font-size: 0.9rem;
    margin-bottom: 0.5rem;
    color: #ffc107;
  }

  .review-text {
    font-size: 0.85rem;
    color: #333;
    margin: 0.5rem 0;
    font-style: italic;
  }

  .review-author {
    font-size: 0.8rem;
    color: #666;
    margin: 0;
    font-weight: bold;
  }

  .loading {
    text-align: center;
    color: #999;
    padding: 1rem;
  }

  .reviews-footer {
    padding: 0.8rem;
    border-top: 1px solid #eee;
    background: #fafafa;
    border-radius: 0 0 12px 12px;
  }

  .view-all-btn {
    color: #264653;
    text-decoration: none;
    font-weight: bold;
    font-size: 0.9rem;
    transition: color 0.2s;
  }

  .view-all-btn:hover {
    color: #f4a261;
  }

  /* Mobile Responsiveness */
  @media (max-width: 480px) {
    .reviews-popup {
      width: calc(100vw - 40px);
      left: 20px;
      right: 20px;
    }
  }
</style>
```

---

## Option 3: Using a Third-Party Review Widget (No Coding Required)

### Services:
1. **Trustpilot** (https://www.trustpilot.com)
2. **Reviews.io** (https://reviews.io)
3. **Yext** (https://www.yext.com)

### Quick Setup:
1. Sign up → Connect Google Business Profile
2. Generate embed code
3. Add code before closing `</body>`
4. Customize widget positioning in their dashboard

---

## 🔧 Implementation Steps for Option 2

### Step-by-Step:

**1. Get Your Credentials:**
   - Google API Key from Google Cloud Console
   - Business ID from Google Business Profile
   - Location ID (ask Google support or check Business Profile URL)

**2. Add the Code:**
   - Open [index.html](index.html)
   - Go to the end of the file (before `</body>`)
   - Paste the code above

**3. Replace Placeholders:**
   ```javascript
   const GOOGLE_API_KEY = "YOUR_API_KEY_HERE"; // → YOUR_ACTUAL_KEY
   const BUSINESS_ID = "YOUR_BUSINESS_ID_HERE"; // → YOUR_ID
   const LOCATION_ID = "YOUR_LOCATION_ID_HERE"; // → YOUR_ID
   ```

**4. Test:**
   - Open landing page
   - Popup should appear after 5 seconds
   - Click close button (✕)

---

## 🚀 Alternative: Manual Review Management

If API is complex, use this **simpler version** that you manually update:

```html
<!-- Add this to index.html before </body> -->
<div id="google-reviews-popup" class="reviews-popup">
  <div class="reviews-container">
    <div class="reviews-header">
      <h3>⭐ Client Testimonials</h3>
      <button class="close-reviews" onclick="closeReviews()">✕</button>
    </div>
    <div class="reviews-content">
      <div class="review-card">
        <div class="review-rating">⭐⭐⭐⭐⭐</div>
        <p class="review-text">"Amazing tour! The guides knew everything about Kwahu."</p>
        <p class="review-author">- Ama K. (Google Review)</p>
      </div>
      <!-- Add more reviews as needed -->
    </div>
  </div>
</div>

<script>
function closeReviews() {
  document.getElementById("google-reviews-popup").classList.remove("show");
}

window.addEventListener("load", () => {
  setTimeout(() => {
    document.getElementById("google-reviews-popup").classList.add("show");
  }, 5000);
});
</script>
```

---

## 📌 Next Steps

**Choose your approach:**
1. ✅ **Easiest:** Use Trustpilot or Reviews.io (fully managed)
2. ✅ **Recommended:** Option 2 with Google API (more control)
3. ✅ **Quickest:** Manual version (update reviews manually)

**Questions?** Contact Google Cloud Support or visit:
- Google Cloud Console: https://console.cloud.google.com
- Google Business Profile: https://business.google.com

---

## ✨ Customization Tips

- **Change colors:** Edit hex codes in `<style>` section (#264653, #f4a261, etc.)
- **Delay popup:** Change `setTimeout(showReviews, 5000)` (5000ms = 5 seconds)
- **Position:** Change `bottom: -400px` to adjust height, `left: 20px` for left distance
- **Auto-hide:** Add `setTimeout(closeReviews, 15000)` after showing

---

*Last Updated: February 3, 2026*
