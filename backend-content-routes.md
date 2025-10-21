# Complete Backend Content Routes & Controllers

## Content Routes (routes/content.js)

```javascript
const express = require('express');
const router = express.Router();
const { protect } = require('../middleware/auth');
const {
    getAllContent,
    getContentById,
    searchContent,
    getMovies,
    getTVShows,
    getContentByGenre,
    getContentByCountry,
    getContentByYear
} = require('../controllers/contentController');

// @route   GET /api/content
// @desc    Get all content with pagination
// @access  Private
router.get('/', protect, getAllContent);

// @route   GET /api/content/movies
// @desc    Get all movies
// @access  Private
router.get('/movies', protect, getMovies);

// @route   GET /api/content/shows
// @desc    Get all TV shows
// @access  Private
router.get('/shows', protect, getTVShows);

// @route   GET /api/content/search
// @desc    Search content by title or description
// @access  Private
router.get('/search', protect, searchContent);

// @route   GET /api/content/genre/:genre
// @desc    Get content by genre
// @access  Private
router.get('/genre/:genre', protect, getContentByGenre);

// @route   GET /api/content/country/:country
// @desc    Get content by country
// @access  Private
router.get('/country/:country', protect, getContentByCountry);

// @route   GET /api/content/year/:year
// @desc    Get content by release year
// @access  Private
router.get('/year/:year', protect, getContentByYear);

// @route   GET /api/content/:id
// @desc    Get content by ID
// @access  Private
router.get('/:id', protect, getContentById);

module.exports = router;
```

## Content Controller (controllers/contentController.js)

```javascript
const NetflixContent = require('../models/NetflixContent');

// @desc    Get all content with pagination
// @route   GET /api/content
// @access  Private
exports.getAllContent = async (req, res, next) => {
    try {
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 20;
        const skip = (page - 1) * limit;

        const total = await NetflixContent.countDocuments();
        const content = await NetflixContent.find()
            .sort({ release_year: -1 })
            .skip(skip)
            .limit(limit);

        res.status(200).json({
            success: true,
            count: content.length,
            total,
            page,
            pages: Math.ceil(total / limit),
            data: content
        });
    } catch (error) {
        console.error('Get all content error:', error);
        next(error);
    }
};

// @desc    Get content by ID
// @route   GET /api/content/:id
// @access  Private
exports.getContentById = async (req, res, next) => {
    try {
        const content = await NetflixContent.findById(req.params.id);

        if (!content) {
            return res.status(404).json({
                success: false,
                message: 'Content not found'
            });
        }

        res.status(200).json({
            success: true,
            data: content
        });
    } catch (error) {
        console.error('Get content by ID error:', error);
        next(error);
    }
};

// @desc    Get all movies
// @route   GET /api/content/movies
// @access  Private
exports.getMovies = async (req, res, next) => {
    try {
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 20;
        const skip = (page - 1) * limit;

        const total = await NetflixContent.countDocuments({ type: 'Movie' });
        const movies = await NetflixContent.find({ type: 'Movie' })
            .sort({ release_year: -1 })
            .skip(skip)
            .limit(limit);

        res.status(200).json({
            success: true,
            count: movies.length,
            total,
            page,
            pages: Math.ceil(total / limit),
            data: movies
        });
    } catch (error) {
        console.error('Get movies error:', error);
        next(error);
    }
};

// @desc    Get all TV shows
// @route   GET /api/content/shows
// @access  Private
exports.getTVShows = async (req, res, next) => {
    try {
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 20;
        const skip = (page - 1) * limit;

        const total = await NetflixContent.countDocuments({ type: 'TV Show' });
        const shows = await NetflixContent.find({ type: 'TV Show' })
            .sort({ release_year: -1 })
            .skip(skip)
            .limit(limit);

        res.status(200).json({
            success: true,
            count: shows.length,
            total,
            page,
            pages: Math.ceil(total / limit),
            data: shows
        });
    } catch (error) {
        console.error('Get TV shows error:', error);
        next(error);
    }
};

// @desc    Search content
// @route   GET /api/content/search
// @access  Private
exports.searchContent = async (req, res, next) => {
    try {
        const { q, type, year, rating } = req.query;
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 20;
        const skip = (page - 1) * limit;

        let query = {};

        // Text search
        if (q) {
            query.$text = { $search: q };
        }

        // Type filter
        if (type) {
            query.type = type;
        }

        // Year filter
        if (year) {
            query.release_year = parseInt(year);
        }

        // Rating filter
        if (rating) {
            query.rating = rating;
        }

        const total = await NetflixContent.countDocuments(query);
        const content = await NetflixContent.find(query)
            .sort({ release_year: -1 })
            .skip(skip)
            .limit(limit);

        res.status(200).json({
            success: true,
            count: content.length,
            total,
            page,
            pages: Math.ceil(total / limit),
            data: content
        });
    } catch (error) {
        console.error('Search content error:', error);
        next(error);
    }
};

// @desc    Get content by genre
// @route   GET /api/content/genre/:genre
// @access  Private
exports.getContentByGenre = async (req, res, next) => {
    try {
        const { genre } = req.params;
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 20;
        const skip = (page - 1) * limit;

        const query = { listed_in: { $regex: genre, $options: 'i' } };

        const total = await NetflixContent.countDocuments(query);
        const content = await NetflixContent.find(query)
            .sort({ release_year: -1 })
            .skip(skip)
            .limit(limit);

        res.status(200).json({
            success: true,
            count: content.length,
            total,
            genre,
            page,
            pages: Math.ceil(total / limit),
            data: content
        });
    } catch (error) {
        console.error('Get content by genre error:', error);
        next(error);
    }
};

// @desc    Get content by country
// @route   GET /api/content/country/:country
// @access  Private
exports.getContentByCountry = async (req, res, next) => {
    try {
        const { country } = req.params;
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 20;
        const skip = (page - 1) * limit;

        const query = { country: { $regex: country, $options: 'i' } };

        const total = await NetflixContent.countDocuments(query);
        const content = await NetflixContent.find(query)
            .sort({ release_year: -1 })
            .skip(skip)
            .limit(limit);

        res.status(200).json({
            success: true,
            count: content.length,
            total,
            country,
            page,
            pages: Math.ceil(total / limit),
            data: content
        });
    } catch (error) {
        console.error('Get content by country error:', error);
        next(error);
    }
};

// @desc    Get content by year
// @route   GET /api/content/year/:year
// @access  Private
exports.getContentByYear = async (req, res, next) => {
    try {
        const { year } = req.params;
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 20;
        const skip = (page - 1) * limit;

        const query = { release_year: parseInt(year) };

        const total = await NetflixContent.countDocuments(query);
        const content = await NetflixContent.find(query)
            .sort({ title: 1 })
            .skip(skip)
            .limit(limit);

        res.status(200).json({
            success: true,
            count: content.length,
            total,
            year: parseInt(year),
            page,
            pages: Math.ceil(total / limit),
            data: content
        });
    } catch (error) {
        console.error('Get content by year error:', error);
        next(error);
    }
};
```

## Error Handler Middleware (middleware/errorHandler.js)

```javascript
// Custom error handler middleware

const errorHandler = (err, req, res, next) => {
    let error = { ...err };
    error.message = err.message;

    // Log to console for dev
    console.error(err);

    // Mongoose bad ObjectId
    if (err.name === 'CastError') {
        const message = 'Resource not found';
        error = { message, statusCode: 404 };
    }

    // Mongoose duplicate key
    if (err.code === 11000) {
        const message = 'Duplicate field value entered';
        error = { message, statusCode: 400 };
    }

    // Mongoose validation error
    if (err.name === 'ValidationError') {
        const message = Object.values(err.errors).map(val => val.message);
        error = { message, statusCode: 400 };
    }

    // JWT errors
    if (err.name === 'JsonWebTokenError') {
        const message = 'Invalid token';
        error = { message, statusCode: 401 };
    }

    if (err.name === 'TokenExpiredError') {
        const message = 'Token expired';
        error = { message, statusCode: 401 };
    }

    res.status(error.statusCode || 500).json({
        success: false,
        error: error.message || 'Server Error'
    });
};

module.exports = errorHandler;
```
