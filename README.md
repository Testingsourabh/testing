// models/RoomType.js
const mongoose = require('mongoose');
const { Schema } = mongoose;

const roomTypeSchema = new Schema({
  // Human-readable identifier for internal use
  code: {
    type: String,
    required: true,
    unique: true,
    uppercase: true,
    trim: true,
    index: true
  },
  
  // Display name for guest-facing interfaces
  name: {
    type: String,
    required: true,
    trim: true
  },
  
  // Detailed description for booking engines
  description: {
    type: String,
    trim: true
  },
  
  // Physical capacity specifications
  capacity: {
    baseOccupancy: {
      type: Number,
      required: true,
      min: 1,
      default: 2
    },
    maxOccupancy: {
      type: Number,
      required: true,
      min: 1
    },
    extraBedsAllowed: {
      type: Number,
      default: 0,
      min: 0
    }
  },
  
  // Base pricing in cents (always store money in smallest unit)
  basePrice: {
    amount: {
      type: Number,
      required: true,
      min: 0
    },
    currency: {
      type: String,
      required: true,
      default: 'USD',
      uppercase: true
    }
  },
  
  // Amenities and features for search/filter
  amenities: [{
    type: String,
    trim: true
  }],
  
  // Room count for quick availability calculations
  totalRooms: {
    type: Number,
    required: true,
    min: 1
  },
  
  // Soft delete support
  isActive: {
    type: Boolean,
    default: true,
    index: true
  }
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true }
});

// Virtual for calculating current availability percentage
roomTypeSchema.virtual('availabilityPercentage').get(function() {
  if (!this.totalRooms) return 0;
  return Math.round((this.availableRooms / this.totalRooms) * 100);
});

// Index for efficient room type searches
roomTypeSchema.index({ name: 'text', description: 'text' });

module.exports = mongoose.model('RoomType', roomTypeSchema);
