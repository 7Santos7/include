#include <iostream>
#include <string>
#include <curl/curl.h> // Library for making HTTP requests

// Callback function for writing HTTP response data
size_t WriteCallback(void* contents, size_t size, size_t nmemb, std::string* response) {
    size_t totalSize = size * nmemb;
    response->append((char*)contents, totalSize);
    return totalSize;
}

int main() {
    // Set up the request parameters
    std::string apiUrl = "https://api.example.com/nft/claim"; // Replace with the actual API endpoint
    std::string nftBoxId = "12345"; // Replace with the ID of the NFT box to claim

    // Create a CURL instance
    CURL* curl = curl_easy_init();
    if (curl) {
        std::string responseData;

        // Set the request URL
        curl_easy_setopt(curl, CURLOPT_URL, apiUrl.c_str());

        // Set the request method to POST
        curl_easy_setopt(curl, CURLOPT_POST, 1L);

        // Set the request payload (if needed)
        std::string postFields = "nftBoxId=" + nftBoxId;
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, postFields.c_str());

        // Set the callback function for writing response data
        curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, WriteCallback);
        curl_easy_setopt(curl, CURLOPT_WRITEDATA, &responseData);

        // Perform the request
        CURLcode res = curl_easy_perform(curl);
        if (res != CURLE_OK) {
            std::cerr << "Failed to perform HTTP request: " << curl_easy_strerror(res) << std::endl;
        } else {
            // Process the response
            std::cout << "NFT box claimed successfully. Response: " << responseData << std::endl;
        }

        // Clean up
        curl_easy_cleanup(curl);
    } else {
        std::cerr << "Failed to initialize CURL" << std::endl;
    }

    return 0;
}
