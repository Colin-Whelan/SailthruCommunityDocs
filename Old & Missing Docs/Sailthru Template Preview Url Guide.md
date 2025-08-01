# Sailthru Template Preview Image URL Generation Guide

This guide explains how to programmatically generate preview image URLs for Sailthru templates, which can be useful for building custom search interfaces, dashboard widgets, or other integrations.

## Overview

Sailthru automatically generates preview images for email templates and stores them on Amazon S3. These images can be accessed directly if you know the correct URL pattern and how to generate the template codes.

## URL Pattern

The preview images follow this consistent URL structure:

```
https://sailthru-html-thumbnail-generator-prod-new.s3.amazonaws.com/[ACCOUNT_ID]/template_[TEMPLATE_CODE].jpg
```

Where:
- `[ACCOUNT_ID]` is your Sailthru account identifier
- `[TEMPLATE_CODE]` is a base-36 encoded version of the template ID

## Account ID Detection

### What is the Account ID?

The account ID is a unique identifier for your Sailthru account. It consists of 3 alpha-numeric characters:
- `123`, `456`, `789`, `abc`, `7xy`, `ts1`

### How to Detect Your Account ID

You can find your account ID by examining existing template preview images on the Sailthru templates page:

```javascript
// Look for existing template images
const existingImages = document.querySelectorAll('img[src*="sailthru-html-thumbnail-generator"]');

// Extract account ID from the first image
if (existingImages.length > 0) {
    const match = existingImages[0].src.match(/sailthru-html-thumbnail-generator[^\/]*\.s3\.amazonaws\.com\/([^\/]+)\//);
    if (match) {
        const accountId = match[1];
        console.log('Your account ID is:', accountId);
    }
}
```

### Robust Detection Function

For web-based tools, you can automatically detect the account ID by checking multiple images to ensure accuracy:

```javascript
function detectAccountId() {
    const existingImages = document.querySelectorAll('img[src*="sailthru-html-thumbnail-generator"]');
    const accountIds = [];
    
    // Check first 5 images
    existingImages.forEach((img, index) => {
        if (index < 5) {
            const match = img.src.match(/sailthru-html-thumbnail-generator[^\/]*\.s3\.amazonaws\.com\/([^\/]+)\//);
            if (match) {
                accountIds.push(match[1]);
            }
        }
    });
    
    if (accountIds.length > 0) {
        // Find the most common account ID (in case of inconsistencies)
        const idCounts = {};
        accountIds.forEach(id => {
            idCounts[id] = (idCounts[id] || 0) + 1;
        });
        
        return Object.keys(idCounts).reduce((a, b) => 
            idCounts[a] > idCounts[b] ? a : b
        );
    }
    
    return null; // No account ID detected
}
```

### When to Use Detection vs. Hardcoding

**Use automatic detection for:**
- **Browser extensions** and userscripts
- **Web-based tools** that run within the Sailthru interface
- **Development and testing** when you're unsure of the account ID

**Use hardcoded values for:**
- **CLI tools** and server-side applications
- **Production applications** where performance is critical
- **Multi-account tools** where you know the account IDs in advance

**Hybrid approach (recommended for production):**
```javascript
const CONFIG = {
    accountId: 'your_known_account_id', // Hardcode for performance
    autoDetect: false // Set to true for development/unknown accounts
};

function getAccountId() {
    if (CONFIG.autoDetect) {
        return detectAccountId() || CONFIG.accountId; // Fallback to hardcoded
    }
    return CONFIG.accountId;
}
```

Depending on your use case, using a configuration option that allows both detection and hardcoding may provide the best balance of flexibility and performance.

## Template Code Generation (Base 36 Conversion)

### What is Base 36?

Base 36 is a positional numeral system using 36 symbols: digits 0-9 and letters a-z. Sailthru uses this encoding to create shorter, URL-friendly template codes from numeric template IDs.

### Why Base 36?

- **Compact**: Reduces long numeric IDs to shorter strings
- **URL-safe**: Uses only alphanumeric characters
- **Reversible**: Can convert back to original ID if needed

### Conversion Examples

| Template ID | Base 36 Code | Full Image URL |
|-------------|--------------|----------------|
| `892847391` | `dqf9yr` | `template_dqf9yr.jpg` |
| `523941762` | `8yz2mq` | `template_8yz2mq.jpg` |
| `759284631` | `cr7h8f` | `template_cr7h8f.jpg` |
| `431852964` | `7k9mn2` | `template_7k9mn2.jpg` |

### JavaScript Implementation

```javascript
// Convert template ID to base 36 code
function generateTemplateCode(templateId) {
    return parseInt(templateId).toString(36);
}

// Example usage
const templateId = '147525898';
const templateCode = generateTemplateCode(templateId); // Returns: '2ftzpm'
```

### Reverse Conversion (Optional)

If you need to convert back from template code to ID:

```javascript
// Convert base 36 code back to template ID
function parseTemplateCode(templateCode) {
    return parseInt(templateCode, 36).toString();
}

// Example usage
const templateCode = '2ftzpm';
const templateId = parseTemplateCode(templateCode); // Returns: '147525898'
```

## Complete URL Generation Function

Here's a complete function that generates preview image URLs:

```javascript
function generatePreviewUrl(templateId, accountId) {
    const templateCode = parseInt(templateId).toString(36);
    return `https://sailthru-html-thumbnail-generator-prod-new.s3.amazonaws.com/${accountId}/template_${templateCode}.jpg`;
}

// Example usage
const templateId = '147525898';
const accountId = 'your_account_id'; // Replace with your actual account ID
const previewUrl = generatePreviewUrl(templateId, accountId);
// Returns: https://sailthru-html-thumbnail-generator-prod-new.s3.amazonaws.com/your_account_id/template_2ftzpm.jpg
```

## Complete Implementation Example

Here's a full example that detects the account ID and generates preview URLs:

```javascript
// Auto-detect account ID and generate preview URL for any template
function getTemplatePreviewUrl(templateId) {
    // Try to detect account ID from existing images
    let accountId = null;
    const existingImages = document.querySelectorAll('img[src*="sailthru-html-thumbnail-generator"]');
    
    if (existingImages.length > 0) {
        const match = existingImages[0].src.match(/sailthru-html-thumbnail-generator[^\/]*\.s3\.amazonaws\.com\/([^\/]+)\//);
        if (match) {
            accountId = match[1];
        }
    }
    
    if (!accountId) {
        console.error('Could not detect account ID. Make sure you\'re on a page with template images.');
        return null;
    }
    
    // Generate template code using base 36
    const templateCode = parseInt(templateId).toString(36);
    
    // Construct and return the full URL
    return `https://sailthru-html-thumbnail-generator-prod-new.s3.amazonaws.com/${accountId}/template_${templateCode}.jpg`;
}

// Usage example
const previewUrl = getTemplatePreviewUrl('147525898');
console.log(previewUrl);
// Output: https://sailthru-html-thumbnail-generator-prod-new.s3.amazonaws.com/[your_account]/template_2ftzpm.jpg
```

## Error Handling

Not all templates may have preview images. Here's how to handle missing images:

```javascript
function createImageWithFallback(templateId, accountId) {
    const previewUrl = generatePreviewUrl(templateId, accountId);
    
    const img = document.createElement('img');
    img.src = previewUrl;
    img.style.width = '50px';
    img.style.height = '50px';
    img.style.objectFit = 'cover';
    
    // Handle missing images
    img.onerror = function() {
        this.style.display = 'none';
        // Show fallback text or icon
        const fallback = document.createElement('span');
        fallback.textContent = 'No preview';
        fallback.style.color = '#999';
        fallback.style.fontSize = '12px';
        this.parentNode.appendChild(fallback);
    };
    
    return img;
}
```

## Use Cases

This URL generation technique can be used for:

- **Custom search interfaces** - Show template previews in search results
- **Dashboard widgets** - Display recent templates with thumbnails
- **Template browsers** - Build custom template selection interfaces
- **Reporting tools** - Include visual previews in template usage reports
- **Browser extensions** - Enhance the Sailthru interface with better preview functionality

## Technical Notes

- **Image format**: All preview images are in JPG format
- **Cache considerations**: Images are cached by S3, so they may take a moment to appear for newly created templates
- **Size optimization**: Consider using appropriate image sizes for your use case to optimize loading performance
- **CORS**: These images are served with proper CORS headers for cross-origin use

## Testing Your Implementation

You can test the URL generation in your browser console:

```javascript
// Test with a known template ID
const testTemplateId = 'YOUR_TEMPLATE_ID_HERE';
const testAccountId = 'YOUR_ACCOUNT_ID_HERE';
const testUrl = `https://sailthru-html-thumbnail-generator-prod-new.s3.amazonaws.com/${testAccountId}/template_${parseInt(testTemplateId).toString(36)}.jpg`;

// Test if the image exists
const testImg = new Image();
testImg.onload = () => console.log('✅ Preview image exists:', testUrl);
testImg.onerror = () => console.log('❌ Preview image not found:', testUrl);
testImg.src = testUrl;
```

This pattern should work consistently across all Sailthru accounts and can be integrated into any custom tools or interfaces you build.
