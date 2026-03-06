# Use official Nginx image
FROM nginx:latest

# Copy website files
COPY website-001 /usr/share/nginx/html

# Expose ports
EXPOSE  80 8080 8443

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -q --spider http://localhost:8080/health || exit 1

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]