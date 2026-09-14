# frozen_string_literal: true

module Api
  module V1
    class BaseController < ActionController::API
      include ActionController::HttpAuthentication::Token::AttributeMethods

      rescue_from ActiveRecord::RecordNotFound, with: :render_not_found
      rescue_from ActionController::ParameterMissing, with: :render_bad_request

      private

      def render_not_found(exception)
        render json: {
          error: {
            message: exception.message || "Resource not found",
            code: "not_found"
          }
        }, status: :not_found
      end

      def render_bad_request(exception)
        render json: {
          error: {
            message: exception.message,
            code: "bad_request"
          }
        }, status: :bad_request
      end
    end
  end
end