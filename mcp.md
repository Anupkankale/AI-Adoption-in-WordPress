{
  "servers": {
    "teal-snake-wordpress": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@automattic/mcp-wordpress-remote@^0.2"
      ],
      "env": {
        "WP_API_URL": "https://########.com/wp-json/mcp/mcp-adapter-default-server",
        "WP_API_USERNAME": "YOUR_USERNAME",
        "WP_API_PASSWORD": "YOUR_APPLICATION_PASSWORD"
      }
    }
  }
}
