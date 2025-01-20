# Use official Nginx image
FROM nginx:1.18

# Copy custom Nginx configuration
COPY devops.conf /etc/nginx/conf.d/

# Expose port 9000 for the web service
EXPOSE 9000

# Define a health check for the service
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD curl --fail http://localhost:9000/health-check || exit 1


# اجرای Nginx
CMD ["nginx", "-g", "daemon off;"]
