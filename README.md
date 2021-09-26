## huffman_algorithm_implementation


### Given a text,a prefix code that minimizes the total number of bits needed to encode the text, 
### it is the encoding that makes the text smaller (fewer bits = more compression).

______________________________________________________________________________________________________________

#include <iostream>
#include <string>
#include <queue>
#include <unordered_map>
using namespace std;
 
#define EMPTY_STRING ""
 
##//Tree Node
struct Node
{
    char ch;
    int freq;
    Node *left, *right;
};
 
##//to allocate a new tree node
Node* getNode(char ch, int freq, Node* left, Node* right)
{
    Node* node = new Node();
 
    node->ch = ch;
    node->freq = freq;
    node->left = left;
    node->right = right;
 
    return node;
}
 
##// Comparison object to be used to order the heap
struct comp
{
    bool operator()(const Node* l, const Node* r) const
    {
        // the highest priority item has the lowest frequency
        return l->freq > r->freq;
    }
};
 
##// Utility function to check if Huffman Tree contains only a single node
bool isLeaf(Node* root) {
    return root->left == nullptr && root->right == nullptr;
}
 
##// Traverse the Huffman Tree and store Huffman Codes in a map.
void encode(Node* root, string str, unordered_map<char, string> &huffmanCode)
{
    if (root == nullptr) {
        return;
    }
 
    // found a leaf node
    if (isLeaf(root)) {
        huffmanCode[root->ch] = (str != EMPTY_STRING) ? str : "1";
    }
 
    encode(root->left, str + "0", huffmanCode);
    encode(root->right, str + "1", huffmanCode);
}
 
##// Traverse the Huffman Tree and decode the encoded string
void decode(Node* root, int &index, string str)
{
    if (root == nullptr) {
        return;
    }
 
 ##// found a leaf node
    if (isLeaf(root))
    {
        cout << root->ch;
        return;
    }
 
    index++;
 
    if (str[index] == '0') {
        decode(root->left, index, str);
    }
    else {
        decode(root->right, index, str);
    }
}
 
##// Builds Huffman Tree and decodes the given input text
void buildHuffmanTree(string text)
{
    
    if (text == EMPTY_STRING) {
        return;
    }
 
   
    unordered_map<char, int> freq;
    for (char ch: text) {
        freq[ch]++;
    }
 
  
    priority_queue<Node*, vector<Node*>, comp> pq;
 
    
    for (auto pair: freq) {
        pq.push(getNode(pair.first, pair.second, nullptr, nullptr));
    }
 
  
    while (pq.size() != 1)
    {
       
 
        Node* left = pq.top(); pq.pop();
        Node* right = pq.top();    pq.pop();
 
       
        int sum = left->freq + right->freq;
        pq.push(getNode('\0', sum, left, right));
    }
 
  
    Node* root = pq.top();
 
 ##// Traverse the Huffman Tree and store Huffman Codes
 ##// in a map. Also, print them
    unordered_map<char, string> huffmanCode;
    encode(root, EMPTY_STRING, huffmanCode);
 
    cout << "Huffman Codes are:\n" << endl;
    for (auto pair: huffmanCode) {
        cout << pair.first << " " << pair.second << endl;
    }
 
    cout << "\nThe original string is:\n" << text << endl;
 
  ##print encoded
    string str;
    for (char ch: text) {
        str += huffmanCode[ch];
    }
 
    cout << "\nThe encoded string is:\n" << str << endl;
    cout << "\nThe decoded string is:\n";
 
    if (isLeaf(root))
    {
        
        while (root->freq--) {
            cout << root->ch;
        }
    }
    else {
        
        int index = -1;
        while (index < (int)str.size() - 1) {
            decode(root, index, str);
        }
    }
}
 
##//main function 
int main()
{
    string text = "This is Huffman Compression";
    buildHuffmanTree(text);
 
    return 0;
}
