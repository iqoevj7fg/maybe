# frozen_string_literal: true

module Api
  module V1
    class BaseController < ApplicationController
      skip_before_action :verify_authenticity_token
      before_action :authenticate_api_user!

      rescue_from ActiveRecord::RecordNotFound, with: :render_not_found
      rescue_from ActiveRecord::RecordInvalid, with: :render_unprocessable_entity

      private

      def authenticate_api_user!
        token = request.headers["Authorization"]&.split(" ")&.last
        @current_user = User.find_by(api_key: token) if token.present?

        render json: { error: "Unauthorized" }, status: :unauthorized unless @current_user
      end

      def render_not_found(exception)
        render json: { error: exception.message }, status: :not_found
      end

      def render_unprocessable_entity(exception)
        render json: { errors: exception.record.errors.full_messages }, status: :unprocessable_entity
      end
    end
  end
end